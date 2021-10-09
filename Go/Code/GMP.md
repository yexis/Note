## -GMP原理与调度

### 一、原理解析

###### go语言的协程

**线程分为“内核态”线程和“用户态”线程，一个“用户态“线程必须要绑定到内核态线程。**

**在Go里面，更加细分。”内核态“线程叫线程(thread)，”用户态“线程叫协程（co-routine）**



goroutine来自协程的概念。**让一组可复用的函数运行在一组线程之上，即使有协程阻塞，该线程的其他协程也可以被runtime调度，转移到其他可运行的线程上**

Goroutine的特点： 

1. 占用内存小（几KB）
2. 调度灵活（runtime调度）

![image-20200707094447153](/home/yex/Desktop/image-20200707094447153.png)

###### 定义

- 全局队列：存放等待运行的G
- P的本地队列：类似全局队列，存放的也是等待运行的G（数量有限，不超过256个）。**新建G'时，G'优先加入到P的本地队列，如果队列满了，则会把本地队列中的一半的G转移到全局队列**
- P列表：在程序启动时创建，保存在数组中，最多GOMAXPROCS个。
- 线程M:线程要运行任务需获取P，从P的本地队列获取G（**P的本地队列为空时，M也会尝试从全局队列拿一批G放到P的本地队列，或从其他P的本地队列偷一半放到自己的本地队列**）。

###### 创建时间

- P：在确定P的最大数量后，运行时系统根据最大数量创建N个P
- M：没有足够的M来关联P并运行其中的可运行的G（如所有M都阻塞，P中还有许多就绪任务，P会去寻找空闲的M，没有空闲的便创建



###### 调度器的设计策略

- 复用线程： 避免频繁的创建、销毁线程。而是对线程的复用

- work stealing：当本线程M无可运行的线程G时，尝试从其他线程绑定的P中偷取线程，而不是销毁线程

- hand off：当线程M因为G进行系统调用而阻塞时，线程M释放绑定的P，把P转交给其他线程绑定执行

- 抢占式：在coroutine中要等待一个协程主动让出CPU才执行下一个协程，**在Go中一个goroutine最多占用CPU 10ms**，防止其他goroutine被饿死，这也是goroutine不同与coroutine的地方

  

###### go func()的调度流程

![image-20200707100041104](/home/yex/Desktop/image-20200707100041104.png)

###### 调度器的生命周期

![image-20200707100235077](/home/yex/Desktop/image-20200707100235077.png)

- **M0**：启动程序后编号为0的主线程，M0对应的实例在全局变量runtime.mo中，不需要在heap上分配。M0负责初始化操作和启动第一个G，在之后M0就和其他M一样了。

- **G0**：**G0负责调度G，G0不指向任何可执行的函数，每个M都会有一个自己的G0**，在调度或系统调用时会使用G0的栈空间。







- schedule() : G0调用该函数寻找可运行的G，并负责协程的切换

- findrunnable() : M尝试从全局队列取一批G放入到P的本地队列

  取G的数量满足下面的公式

  > n = min(len(GQ)/GOMAXPROCS + 1, len(GQ/2))

至少从全局队列取 1 个 g，但每次不要从全局队列移动太多的 g 到 p 本地队列，给其他 p 留点。这是从全局队列到 P 本地队列的负载均衡。

---

### 二、场景分析

<img src="http://www.topgoer.com/static/7.1/gmp/22.jpg" alt="img" style="zoom: 50%;" />

<img src="http://www.topgoer.com/static/7.1/gmp/23.jpg" alt="img" style="zoom:50%;" />

<img src="http://www.topgoer.com/static/7.1/gmp/24.jpg" alt="img" style="zoom:50%;" />

<img src="http://www.topgoer.com/static/7.1/gmp/25.jpg" alt="img" style="zoom:50%;" />

<img src="http://www.topgoer.com/static/7.1/gmp/26.jpg" alt="img" style="zoom:50%;" />



<img src="http://www.topgoer.com/static/7.1/gmp/27.jpg" alt="img" style="zoom:50%;" />

<img src="http://www.topgoer.com/static/7.1/gmp/28.jpg" alt="img" style="zoom:50%;" />

<img src="http://www.topgoer.com/static/7.1/gmp/29.jpg" alt="img" style="zoom:50%;" />

<img src="http://www.topgoer.com/static/7.1/gmp/30.jpg" alt="img" style="zoom:50%;" />

<img src="http://www.topgoer.com/static/7.1/gmp/31.jpg" alt="img" style="zoom:50%;" />

<img src="http://www.topgoer.com/static/7.1/gmp/32.jpg" alt="img" style="zoom:50%;" />

---

### 三、源码分析

**线程m：**

```go
type m struct {
	g0      *g     // goroutine with scheduling stack
	morebuf gobuf  // gobuf arg to morestack
	divmod  uint32 // div/mod denominator for arm - known to liblink

	// Fields not known to debuggers.
	procid        uint64       // for debuggers, but offset not hard-coded
	gsignal       *g           // signal-handling g
	goSigStack    gsignalStack // Go-allocated signal handling stack
	sigmask       sigset       // storage for saved signal mask
	tls           [6]uintptr   // thread-local storage (for x86 extern register)
	mstartfn      func()
	curg          *g       // current running goroutine
	caughtsig     guintptr // goroutine running during fatal signal
	p             puintptr // attached p for executing go code (nil if not executing go code)
	nextp         puintptr
	oldp          puintptr // the p that was attached before executing a syscall
	id            int64
	mallocing     int32
	throwing      int32
	preemptoff    string // if != "", keep curg running on this m
	locks         int32
	dying         int32
	profilehz     int32
	spinning      bool // m is out of work and is actively looking for work
	blocked       bool // m is blocked on a note
	newSigstack   bool // minit on C thread called sigaltstack
	printlock     int8
	incgo         bool   // m is executing a cgo call
	freeWait      uint32 // if == 0, safe to free g0 and delete m (atomic)
	fastrand      [2]uint32
	needextram    bool
	traceback     uint8
	ncgocall      uint64      // number of cgo calls in total
	ncgo          int32       // number of cgo calls currently in progress
	cgoCallersUse uint32      // if non-zero, cgoCallers in use temporarily
	cgoCallers    *cgoCallers // cgo traceback if crashing in cgo call
	park          note
	alllink       *m // on allm
	schedlink     muintptr
	mcache        *mcache
	lockedg       guintptr
	createstack   [32]uintptr // stack that created this thread.
	lockedExt     uint32      // tracking for external LockOSThread
	lockedInt     uint32      // tracking for internal lockOSThread
	nextwaitm     muintptr    // next m waiting for lock
	waitunlockf   func(*g, unsafe.Pointer) bool
	waitlock      unsafe.Pointer
	waittraceev   byte
	waittraceskip int
	startingtrace bool
	syscalltick   uint32
	freelink      *m // on sched.freem

	// these are here because they are too large to be on the stack
	// of low-level NOSPLIT functions.
	libcall   libcall
	libcallpc uintptr // for cpu profiler
	libcallsp uintptr
	libcallg  guintptr
	syscall   libcall // stores syscall parameters on windows

	vdsoSP uintptr // SP for traceback while in VDSO call (0 if not in call)
	vdsoPC uintptr // PC for traceback while in VDSO call

	// preemptGen counts the number of completed preemption
	// signals. This is used to detect when a preemption is
	// requested, but fails. Accessed atomically.
	preemptGen uint32

	// Whether this is a pending preemption signal on this M.
	// Accessed atomically.
	signalPending uint32

	dlogPerM

	mOS
}

```

**m的字段比较多，其中最重要的为下面几个：**

```go
g0            *g       //系统在启动时创建，用于执行一些任务
mstartfn      func()   //m的起始函数，go语句携带的函数
curg          *g       //存放当前正在运行的G的指针 
p             guintptr //指向当前与m关联的p
nextp         guintptr //用于暂存于当前M有潜在关联的p
spinning      bool     //当前m是否正在寻找p，自旋状态
lockedg       *g       //与当前m锁定的g
```

---

**调度器P**

```go
type p struct {
	id          int32
	status      uint32 // one of pidle/prunning/...
	link        puintptr
	schedtick   uint32     // incremented on every scheduler call
	syscalltick uint32     // incremented on every system call
	sysmontick  sysmontick // last tick observed by sysmon
	m           muintptr   // back-link to associated m (nil if idle)
	mcache      *mcache
	pcache      pageCache
	raceprocctx uintptr

	deferpool    [5][]*_defer // pool of available defer structs of different sizes (see panic.go)
	deferpoolbuf [5][32]*_defer

	// Cache of goroutine ids, amortizes accesses to runtime·sched.goidgen.
	goidcache    uint64
	goidcacheend uint64

	// Queue of runnable goroutines. Accessed without lock.
	runqhead uint32
	runqtail uint32
	runq     [256]guintptr
	// runnext, if non-nil, is a runnable G that was ready'd by
	// the current G and should be run next instead of what's in
	// runq if there's time remaining in the running G's time
	// slice. It will inherit the time left in the current time
	// slice. If a set of goroutines is locked in a
	// communicate-and-wait pattern, this schedules that set as a
	// unit and eliminates the (potentially large) scheduling
	// latency that otherwise arises from adding the ready'd
	// goroutines to the end of the run queue.
	runnext guintptr

	// Available G's (status == Gdead)
	gFree struct {
		gList
		n int32
	}

	sudogcache []*sudog
	sudogbuf   [128]*sudog

	// Cache of mspan objects from the heap.
	mspancache struct {
		// We need an explicit length here because this field is used
		// in allocation codepaths where write barriers are not allowed,
		// and eliminating the write barrier/keeping it eliminated from
		// slice updates is tricky, moreso than just managing the length
		// ourselves.
		len int
		buf [128]*mspan
	}

	tracebuf traceBufPtr

	// traceSweep indicates the sweep events should be traced.
	// This is used to defer the sweep start event until a span
	// has actually been swept.
	traceSweep bool
	// traceSwept and traceReclaimed track the number of bytes
	// swept and reclaimed by sweeping in the current sweep loop.
	traceSwept, traceReclaimed uintptr

	palloc persistentAlloc // per-P to avoid mutex

	_ uint32 // Alignment for atomic fields below

	// The when field of the first entry on the timer heap.
	// This is updated using atomic functions.
	// This is 0 if the timer heap is empty.
	timer0When uint64

	// Per-P GC state
	gcAssistTime         int64    // Nanoseconds in assistAlloc
	gcFractionalMarkTime int64    // Nanoseconds in fractional mark worker (atomic)
	gcBgMarkWorker       guintptr // (atomic)
	gcMarkWorkerMode     gcMarkWorkerMode

	// gcMarkWorkerStartTime is the nanotime() at which this mark
	// worker started.
	gcMarkWorkerStartTime int64

	// gcw is this P's GC work buffer cache. The work buffer is
	// filled by write barriers, drained by mutator assists, and
	// disposed on certain GC state transitions.
	gcw gcWork

	// wbBuf is this P's GC write barrier buffer.
	//
	// TODO: Consider caching this in the running G.
	wbBuf wbBuf

	runSafePointFn uint32 // if 1, run sched.safePointFn at next safe point

	// Lock for timers. We normally access the timers while running
	// on this P, but the scheduler can also do it from a different P.
	timersLock mutex

	// Actions to take at some time. This is used to implement the
	// standard library's time package.
	// Must hold timersLock to access.
	timers []*timer

	// Number of timers in P's heap.
	// Modified using atomic instructions.
	numTimers uint32

	// Number of timerModifiedEarlier timers on P's heap.
	// This should only be modified while holding timersLock,
	// or while the timer status is in a transient state
	// such as timerModifying.
	adjustTimers uint32

	// Number of timerDeleted timers in P's heap.
	// Modified using atomic instructions.
	deletedTimers uint32

	// Race context used while executing timer functions.
	timerRaceCtx uintptr

	// preempt is set to indicate that this P should be enter the
	// scheduler ASAP (regardless of what G is running on it).
	preempt bool

	pad cpu.CacheLinePad
}
```

**P中比较重要的成员**

```go
status            uint32          //p的当前状态
link              guintptr        //下一个p，p在链表结构中时会使用
m                 muintptr        //拥有这个p的m
mcache            *mcache         //分配内存时使用的本地分配器
runqhead          uint32          //本地运行队列的出队序号
runqtail          uint32          //本地运行队列的入队序号
runq              [256]guintptr   //本地运行队列的数组，可以保存256个g
runnext           guintptr        //下一个运行的g
gFree struct {                    
		gList                     //g的自由列表，状态变为_Gdead后可复用的g
		n int32
	}                             
gcBgMarkWorker     guintptr       //后台GC的worker函数，如果它存在M会优先执行它
gcw                gcWork         //GC的本地工作队列
```

---

**协程g**

```go
type g struct {
	// Stack parameters.
	// stack describes the actual stack memory: [stack.lo, stack.hi).
	// stackguard0 is the stack pointer compared in the Go stack growth prologue.
	// It is stack.lo+StackGuard normally, but can be StackPreempt to trigger a preemption.
	// stackguard1 is the stack pointer compared in the C stack growth prologue.
	// It is stack.lo+StackGuard on g0 and gsignal stacks.
	// It is ~0 on other goroutine stacks, to trigger a call to morestackc (and crash).
	stack       stack   // offset known to runtime/cgo
	stackguard0 uintptr // offset known to liblink
	stackguard1 uintptr // offset known to liblink

	_panic       *_panic // innermost panic - offset known to liblink
	_defer       *_defer // innermost defer
	m            *m      // current m; offset known to arm liblink
	sched        gobuf
	syscallsp    uintptr        // if status==Gsyscall, syscallsp = sched.sp to use during gc
	syscallpc    uintptr        // if status==Gsyscall, syscallpc = sched.pc to use during gc
	stktopsp     uintptr        // expected sp at top of stack, to check in traceback
	param        unsafe.Pointer // passed parameter on wakeup
	atomicstatus uint32
	stackLock    uint32 // sigprof/scang lock; TODO: fold in to atomicstatus
	goid         int64
	schedlink    guintptr
	waitsince    int64      // approx time when the g become blocked
	waitreason   waitReason // if status==Gwaiting

	preempt       bool // preemption signal, duplicates stackguard0 = stackpreempt
	preemptStop   bool // transition to _Gpreempted on preemption; otherwise, just deschedule
	preemptShrink bool // shrink stack at synchronous safe point

	// asyncSafePoint is set if g is stopped at an asynchronous
	// safe point. This means there are frames on the stack
	// without precise pointer information.
	asyncSafePoint bool

	paniconfault bool // panic (instead of crash) on unexpected fault address
	gcscandone   bool // g has scanned stack; protected by _Gscan bit in status
	throwsplit   bool // must not split stack
	// activeStackChans indicates that there are unlocked channels
	// pointing into this goroutine's stack. If true, stack
	// copying needs to acquire channel locks to protect these
	// areas of the stack.
	activeStackChans bool

	raceignore     int8     // ignore race detection events
	sysblocktraced bool     // StartTrace has emitted EvGoInSyscall about this goroutine
	sysexitticks   int64    // cputicks when syscall has returned (for tracing)
	traceseq       uint64   // trace event sequencer
	tracelastp     puintptr // last P emitted an event for this goroutine
	lockedm        muintptr
	sig            uint32
	writebuf       []byte
	sigcode0       uintptr
	sigcode1       uintptr
	sigpc          uintptr
	gopc           uintptr         // pc of go statement that created this goroutine
	ancestors      *[]ancestorInfo // ancestor information goroutine(s) that created this goroutine (only used if debug.tracebackancestors)
	startpc        uintptr         // pc of goroutine function
	racectx        uintptr
	waiting        *sudog         // sudog structures this g is waiting on (that have a valid elem ptr); in lock order
	cgoCtxt        []uintptr      // cgo traceback context
	labels         unsafe.Pointer // profiler labels
	timer          *timer         // cached timer for time.Sleep
	selectDone     uint32         // are we participating in a select and did someone win the race?

	// Per-G GC state

	// gcAssistBytes is this G's GC assist credit in terms of
	// bytes allocated. If this is positive, then the G has credit
	// to allocate gcAssistBytes bytes without assisting. If this
	// is negative, then the G must correct this by performing
	// scan work. We track this in bytes to make it fast to update
	// and check for debt in the malloc hot path. The assist ratio
	// determines how this corresponds to scan work debt.
	gcAssistBytes int64
}
```

**g中比较重要的成员**

```go
stack            stack          //当前g使用的栈空间，有lo和hi两个值
stackguard0      uintptr        //检查栈空间是否足够的值，低于这个值会扩张栈 0是go代码使用
stackguard1      uintptr        //检查栈空间是否足够的值，低于这个值会扩张栈 1是原生代码使用
m                *m             //当前g 对应的m
sched            gobuf          //g的调度数据，当g中断时会保存当前的pc和rsp在这里，恢复运行从这里取值
atomicstatus     uint32         //g的当前状态 
schedlink        guintptr       //下一个g，当g在链表中会使用
preempt          bool           //g是否被抢占中
lockedm          muintptr       //g是否要求回到这个m执行，有时候g中断了会要求回到原来的m上执行
param            unsaf3.Pointer //用于传递参数，睡眠时其他goroutine可以设置param，唤醒时该goroutine可以获取
waitsince        int64          //g被阻塞的大体时间

```

---

**sudog**

对goroutine和带发送接收数据的封装，一般当goroutine在等待队列中会使用。

```go
type sudog struct {
	// The following fields are protected by the hchan.lock of the
	// channel this sudog is blocking on. shrinkstack depends on
	// this for sudogs involved in channel ops.

	g *g

	// isSelect indicates g is participating in a select, so
	// g.selectDone must be CAS'd to win the wake-up race.
	isSelect bool
	next     *sudog
	prev     *sudog
	elem     unsafe.Pointer // data element (may point to stack)

	// The following fields are never accessed concurrently.
	// For channels, waitlink is only accessed by g.
	// For semaphores, all fields (including the ones above)
	// are only accessed when holding a semaRoot lock.

	acquiretime int64
	releasetime int64
	ticket      uint32
	parent      *sudog // semaRoot binary tree
	waitlink    *sudog // g.waiting list or semaRoot
	waittail    *sudog // semaRoot
	c           *hchan // channel
}
```

