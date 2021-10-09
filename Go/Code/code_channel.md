### go channel源码分析

Go的底层channel的实现是基于一个使用双向链表实现的循环队列



#### 一、通道是什么?

> 通道是可以让一个goroutine发送特定值到另一个goroutine的通信机制

![img](http://www.topgoer.com/static/7.1/3.png)

**无缓冲通道：**

> 也称同步通道，无缓冲通道上的发送操作将会阻塞，直到另一个goroutine在对应的通道上执行接收操作，此时传送完成，两个goroutine才能继续执行。反之亦然。

![img](http://www.topgoer.com/static/7.1/4.png)

**缓冲通道：**

> 缓冲队列有一个缓冲队列，队列的最大长度在创建的时候通过make的容量参数来设置。
>
> 缓冲通道上的发送操作在队列的尾部插入一个元素，接收操作从队列的头部移除一个元素。
>
> 如果通道满了，发送操作的goroutine会阻塞在通道的等待发送队列中，直到另一个goroutine接收数据。
>
> 如果通道为空，接收操作的goroutine会阻塞在通道的等待接收队列中，直到另一个goroutine发送数据。



#### 二、通道源码剖析

1. ###### channel的构造：

   构造语句，make(chan int)会被golang编译器编译成runtime.makechan函数

   函数原型：

   ```
   func makechan(t *chantype, size int ) *hchan
   ```

   其中，`t *chantype`即构造channel时传入的元素类型。`size int`即用户指定的channel缓冲区大小，不指定则为0。该函数的返回值是`*hchan`。hchan则是channel在golang中的内部实现

   

2. ###### 通道结构 hchan

   ![image-20200712165318132](/home/yex/.config/Typora/typora-user-images/image-20200712165318132.png)

   ```go
   type hchan struct {
       qcount   uint           // buffer中已放入的元素个数
       dataqsiz uint           // 用户构造channel时指定的buf大小
       buf      unsafe.Pointer // buffer
       elemsize uint16         // buffer中每个元素的大小
       closed   uint32         // channel是否关闭，== 0代表未closed
       elemtype *_type         // channel元素的类型信息
       sendx    uint           // buffer中已发送的索引位置 send index
       recvx    uint           // buffer中已接收的索引位置 receive index
       recvq    waitq          // 队列：等待接收的goroutine  list of recv waiters
       sendq    waitq          // 队列：等待发送的goroutine list of send waiters
       
       lock mutex              // 锁
   }
   ```

   通过分析hchan的属性，得知 buffer和waitq是两个重要的组件，hchan的所有行为都是围绕这两个组件进行

   > sudog：对当前运行的goroutine和待发送数据的封装，有一个前驱指针和后驱指针hchan的sendq和recvq是由sudog组成的双向链表。

###### 3.channel中ring buffer的实现

环形缓冲区好处较多，非常适用于FIFO式的固定长度队列

![image-20200708102145564](/home/yex/Desktop/image-20200708102145564.png)



hchan中有两个与buffer相关的变量：recvx和sendx。

- sendx：表示buffer中可写的index
- recvx：表示buffer中可读的index

从recvx到sendx之间的元素，表示已经正常放入buffer中的数据。

###### 4. gopark()和goready()

- **gopark()：**用于协程的切换

  gopark函数做的事情分为两点：

  1. 解除当前goroutine与m的绑定关系，将当前goroutine的状态置为等待状态
  2. 调用一次schedule()函数，在局部调度器P发起一轮新的调度

```go
//gopark()
func gopark(unlockf func(*g, unsafe.Pointer) bool, lock unsafe.Pointer, reason waitReason, traceEv byte, traceskip int) {
    //如果阻塞原因是 Sleep
	if reason != waitReasonSleep {
		checkTimeouts() 
	}
	mp := acquirem()
	gp := mp.curg
	status := readgstatus(gp)
    //如果状态不是_Grunning
	if status != _Grunning && status != _Gscanrunning {
		throw("gopark: bad g status")
	}
    //记录g休眠的原因和上下文
	mp.waitlock = lock
	mp.waitunlockf = unlockf
	gp.waitreason = reason
	mp.waittraceev = traceEv
	mp.waittraceskip = traceskip
    //释放线程m
	releasem(mp)
    // mcall()作用：
    // 1.切换当前线程的堆栈从 g 的堆栈切换到g0的堆栈
    // 2.在g0的堆栈上执行新的函数 fn(g)
    // 3.保存当前协程的信息，当后续唤醒当前信息时恢复现场
	mcall(park_m)
}
//park_m()
func park_m(gp *g) {
    //获取g0
	_g_ := getg()
	if trace.enabled {
		traceGoPark(_g_.m.waittraceev, _g_.m.waittraceskip)
	}
    //更新gp的状态为_Gwaiting
	casgstatus(gp, _Grunning, _Gwaiting)
    //移除gp和m的绑定关系
	dropg()
    //进入等待状态前执行前置函数
	if fn := _g_.m.waitunlockf; fn != nil {
		ok := fn(gp, _g_.m.waitlock)//执行进入wait前的前置函数
		_g_.m.waitunlockf = nil
		_g_.m.waitlock = nil
        //如果waitunlockf函数执行失败，将gp重新置为_Grunnable状态，恢复
		if !ok {
			if trace.enabled {
				traceGoUnpark(gp, 2)
			}
			casgstatus(gp, _Gwaiting, _Grunnable)
			execute(gp, true) // Schedule it back, never returns.
		}
	}
    //发起一轮新的调度
	schedule()
}
```

- **goready()：**用于唤醒协程

  goready主要做的事情：唤醒某一个goroutine，并将该协程转换为runnable状态，并将其放入到P的local queue（本地队列），等待调度wating

```go
func goready(gp *g, traceskip int) {
    //切换到g0的栈
	systemstack(func() {
		ready(gp, traceskip, true)
	})
}

func ready(gp *g, traceskip int, next bool) {
	if trace.enabled {
		traceGoUnpark(gp, traceskip)
	}
    //获取goroutine的运行状态
	status := readgstatus(gp)

    //获取g0
	_g_ := getg()
    //获取m
	mp := acquirem() // disable preemption because it can be holding p in a local var
	if status&^_Gscan != _Gwaiting {
		dumpgstatus(gp)
		throw("bad g->status in ready")
	}
	casgstatus(gp, _Gwaiting, _Grunnable)
    // 将g放入P的可运行队列中
	runqput(_g_.m.p.ptr(), gp, next)
    // 如果有空闲的p 并且没有正在spinning状态的m 则唤醒一个p
	if atomic.Load(&sched.npidle) != 0 && atomic.Load(&sched.nmspinning) == 0 {
		wakep()
	}
	releasem(mp)
}
```



###### 5.向通道中发送数据

​      c<-1 对应于runtime中的runtime.chansend函数 

- memmove() 进行数据的转移，本质上就是一个内存拷贝。

- 发送数据到通道

```go
/*
c：  通道指针
ep： 指向要发送数据的首地址
block：代表写入操作是否阻塞
*/
func chansend(c *hchan, ep unsafe.Pointer, block bool, callerpc uintptr) bool {
	//如果channel为空或者未初始化
    if c == nil {
        //如果block表示非阻塞，直接return
		if !block {
			return false
		}
        //如果block为阻塞，永久阻塞
		gopark(nil, nil, waitReasonChanSendNilChan, traceEvGoStop, 2)
		throw("unreachable")
	}

	if debugChan {
		print("chansend: chan=", c, "\n")
	}

	if raceenabled {
		racereadpc(c.raceaddr(), callerpc, funcPC(chansend))
	}
    //如果block为非阻塞
    //且channel未关闭
    //且（channel非缓冲队列且receiver为空）或者（channel为有缓冲队列且队列已满 ）
    //直接return false
	if !block && c.closed == 0 && ((c.dataqsiz == 0 && c.recvq.first == nil) ||
		(c.dataqsiz > 0 && c.qcount == c.dataqsiz)) {
		return false
	}

	var t0 int64
	if blockprofilerate > 0 {
		t0 = cputicks()
	}
    //获取同步锁
	lock(&c.lock)
    //如果通道已经关闭，写入数据产生panic
	if c.closed != 0 {
		unlock(&c.lock)
		panic(plainError("send on closed channel"))
	}
    
    //*******主要部分*******//
 
    //如果接收队列recvq不为空，即有goroutine在接收队列中等待时
    //不需要区分有缓存的和无缓存的channel
    //跳过缓冲区buf，直接将数据发送给等待的接收者goroutine
	if sg := c.recvq.dequeue(); sg != nil {
        //send函数
		send(c, sg, ep, func() { unlock(&c.lock) }, 3)
		return true
	}
    //如果接收队列recvq为空
    //且缓冲区数据大小 < 通道的大小  (说明此时发送队列sendq一定为空)
	if c.qcount < c.dataqsiz {
		// Space is available in the channel buffer. Enqueue the element to send.
        //直接将数据放入到缓冲区
		qp := chanbuf(c, c.sendx)
		if raceenabled {
			raceacquire(qp)
			racerelease(qp)
		}
        //数据转移：本质上是内存拷贝，将数据从ep处拷贝到qp
		typedmemmove(c.elemtype, qp, ep)
		c.sendx++
		if c.sendx == c.dataqsiz {
			c.sendx = 0
		}
		c.qcount++
		unlock(&c.lock)
		return true
	}
    //如果以上条件均不满足，即
    //接收队列recv不为空，且缓冲区数据量大小 == 通道大小 （即缓冲区buf已满）
    //如果block为非阻塞，解锁并返回
	if !block {
		unlock(&c.lock)
		return false
	}
    //如果block为阻塞
    //如果接收队列recv为空
    //且缓冲队列已满
    //则将当前的goroutine加入到sendq队列
	gp := getg()
	mysg := acquireSudog()
	mysg.releasetime = 0
	if t0 != 0 {
		mysg.releasetime = -1
	}
	mysg.elem = ep
	mysg.waitlink = nil
	mysg.g = gp
	mysg.isSelect = false
	mysg.c = c
	gp.waiting = mysg
	gp.param = nil
    //将当前goroutine的sudog加入到sendq
	c.sendq.enqueue(mysg)
    //将当前goroutine休眠
	gopark(chanparkcommit, unsafe.Pointer(&c.lock), waitReasonChanSend, traceEvGoBlockSend, 2)
	KeepAlive(ep)
	if mysg != gp.waiting {
		throw("G waiting list is corrupted")
	}
	gp.waiting = nil
	gp.activeStackChans = false
	if gp.param == nil {
		if c.closed == 0 {
			throw("chansend: spurious wakeup")
		}
		panic(plainError("send on closed channel"))
	}
	gp.param = nil
	if mysg.releasetime > 0 {
		blockevent(mysg.releasetime-t0, 2)
	}
	mysg.c = nil
	releaseSudog(mysg)
	return true
}
```

简单来说，向通道中发送数据的整个流程如下：

> 1.检查recvq是否为空。如果不为空，则从recvq头部取一个goroutine，将数据发送过去，并唤醒对应的goroutine即可。
>
> 2.如果recv为空，且缓冲取未满，则将数据放入channel buffer缓冲区中。
>
> 3.如果buffer已满，则将要发送的数据和当前goroutine打包成sudog对象放入到sendq中，并将goroutine置为wating状态

**注意： channel的整个发送过程和接收过程都使用了runtime.mutex进行加锁。runtime.mutex是runtime相关源码中常用的一个轻量级锁。**

---

###### 6.向通道中接收数据 

```go
/*
block:表示当channel无法返回数据时是否阻塞等待  比如： 当block为false并且channel中没有数据时，直接返回
*/
func chanrecv(c *hchan, ep unsafe.Pointer, block bool) (selected, received bool) {

    //*******前置场景*******//
    
	if debugChan {
		print("chanrecv: chan=", c, "\n")
	}
    //如果通道为空或未初始化
	if c == nil {
        //如果block为非阻塞
        //直接返回
		if !block {
			return
		}
        //如果block为阻塞，调用gopark()阻塞当前goroutine并抛出异常
		gopark(nil, nil, waitReasonChanReceiveNilChan, traceEvGoStop, 2)
		throw("unreachable")
	}
    //如果通道不为空或者已经初始化
    //如果block为非阻塞
    //且（通道为无缓冲通道且发送对列为空） 或者 （通道为有缓冲通道且缓冲区元素为0且通道未关闭）
    //直接return
	if !block && (c.dataqsiz == 0 && c.sendq.first == nil ||
		c.dataqsiz > 0 && atomic.Loaduint(&c.qcount) == 0) &&
		atomic.Load(&c.closed) == 0 {
		return
	}

	var t0 int64
	if blockprofilerate > 0 {
		t0 = cputicks()
	}
    //获取全局锁
	lock(&c.lock)
    
    //*******主要部分*******//
    
    //如果通道已经关闭
    //且缓冲区中无元素空值
    //直接返回success和空值
	if c.closed != 0 && c.qcount == 0 {
		if raceenabled {
			raceacquire(c.raceaddr())
		}
		unlock(&c.lock)
		if ep != nil {
            //返回空值
			typedmemclr(c.elemtype, ep)
		}
		return true, false
	}

    //如果等待发送对列sendq不为空   （注意：此时的缓冲区一定是满的）
    //直接从等待发送队列sendq中接收数据 
    //返回true,true
	if sg := c.sendq.dequeue(); sg != nil {
		recv(c, sg, ep, func() { unlock(&c.lock) }, 3)
		return true, true
	}
    //如果等待发送队列为空
    //且通道非空
    //则从通道中获取数据
	if c.qcount > 0 {
		qp := chanbuf(c, c.recvx)
		if raceenabled {
			raceacquire(qp)
			racerelease(qp)
		}
		if ep != nil {
            //内存拷贝
			typedmemmove(c.elemtype, ep, qp)
		}
		typedmemclr(c.elemtype, qp)
		c.recvx++
		if c.recvx == c.dataqsiz {
			c.recvx = 0
		}
		c.qcount--
		unlock(&c.lock)
		return true, true
	}
    //如果等待发送队列sendq为空
    //且通道中元素个数为0,通道为空
    //且block为非阻塞
    //则直接返回false，false
	if !block {
		unlock(&c.lock)
		return false, false
	}

    //如果等待发送队列sendq为空
    //且通道为空
    //且block为阻塞
    //则将该goroutine阻塞
	gp := getg()
	mysg := acquireSudog()
	mysg.releasetime = 0
	if t0 != 0 {
		mysg.releasetime = -1
	}
	// No stack splits between assigning elem and enqueuing mysg
	// on gp.waiting where copystack can find it.
	mysg.elem = ep
	mysg.waitlink = nil
	gp.waiting = mysg
	mysg.g = gp
	mysg.isSelect = false
	mysg.c = c
	gp.param = nil
	c.recvq.enqueue(mysg)
	gopark(chanparkcommit, unsafe.Pointer(&c.lock), waitReasonChanReceive, traceEvGoBlockRecv, 2)

	// someone woke us up
	if mysg != gp.waiting {
		throw("G waiting list is corrupted")
	}
	gp.waiting = nil
	gp.activeStackChans = false
	if mysg.releasetime > 0 {
		blockevent(mysg.releasetime-t0, 2)
	}
	closed := gp.param == nil
	gp.param = nil
	mysg.c = nil
	releaseSudog(mysg)
	return true, !closed
}
```

- ep：接收数据变量对应的地址
- sg：从sendq中取出的第一个sudog
- `typedmemmove(c.elemtype, ep, qp)`表示buffer中的当前可读元素拷贝到接收变量的地址处。
- `typedmemmove(c.elemtype, qp, sg.elem)`表示将sendq中goroutine等待发送的数据拷贝到buffer中。因为此后进行了`recv++`, 因此相当于把sendq中的数据放到了队尾。

简单来说，向通道中发送数据的整个流程如下：

> 1.检查sendq是否为空。如果不为空，则从sendq头部取一个goroutine，将缓冲区中队首的元素拷贝给接收变量，同时将sendq中的元素拷贝到队尾。
>
> 2.如果sendq为空，且缓冲取不为空，则直接从缓冲取队首获取元素。
>
> 3.如果缓冲区为空，则将当前goroutine打包成sudog对象放入到recvq中，并将goroutine置为wating状态

简单来说，这里channel将buffer中队首的数据拷贝给了对应的接收变量，同时将sendq中的元素拷贝到了队尾，这样才可以做到数据的FIFO(先入先出)。

接下来可能有点绕，`c.sendx = c.recvx`, 这句话实际的作用相当于`c.sendx = (c.sendx+1) % c.dataqsiz`，因为此时buffer依然是满的，所以`sendx == recvx`是成立的。























---

###### Question

1. 使用channel之前没有make，会出现dead lock错误，为什么？
2. Golang channel源码中的block是什么意思？

