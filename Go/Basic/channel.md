```go
package main

import (
	"fmt"
)

func main() {
	//声明
	/*var ch chan int
	//初始化之后才能使用
	//make(chan 元素类型,[缓冲大小])
	ch <- 10
	x := <- ch
	close(ch)
	fmt.Println(x)*/


	//testchan()
	//testchan2()
	//testchan3()
	testchan4()
}

//宗旨：通过通信共享内存，而不是通过共享内存实现通信

//如果说goroutine是Go程序并发的执行体，channel就是他们之间的连接。channel是可以让一个goroutine发送特定值到另一个goroutine的通信机制

/*
* channel是一种特殊的类型，像一个传送带或队列，总是遵循先入先出的规则，fifo
* 每一个channel都是一个具体类型的导管，声明channel时需要为其指定元素类型
* 格式： var 变量 chan 元素类型
 */


//无缓冲的通道:又称为阻塞的通道或 同步通道
/*
* 下面代码为什么回出现错误?
* 因为创建的是无缓冲的通道，无缓冲的通道只有在有人接受值时才能发送值
* 简单来说，无缓冲的通道必须有接收才能发送
 */
func testchan () {
	ch := make(chan int)
	go recv(ch)
	ch <- 10 // 死锁
	//go recv(ch)
	//time.Sleep(time.Second * 3)
	fmt.Println("发送成功")
}
func recv(c chan int) {
	res := <- c
	fmt.Println("接收成功",res)
}

//有缓冲的通道:使用make函数初始化的时候指定容量
/*
* 通道的容量表示通道中能存放的元素数量
* len(ch chan) 获取通道内元素的数量
* cap(ch chan) 获取通道的容量 // 不变?
× close(ch chan) 关闭channel 不存值或取值的时候一定要关闭通道
 */
func testchan2() {
	var ch chan int
	ch = make(chan int ,1)
	fmt.Println(len(ch))
	fmt.Println(cap(ch))
	ch <- 10
	fmt.Println(len(ch))
	fmt.Println(cap(ch))
	fmt.Println("发送成功")
}

//通过内置的函数close()关闭channel
func testchan3() {
	c := make(chan int)
	go func(chan int) {
		for {
			i := <- c
			fmt.Println(i)
		}
	}(c)
	for i:=0;i < 5;i++ {
		c <- i
	}
	fmt.Println("over")
	//主goroutine结束，其他goroutine自然结束
}

//优雅地从通道中取值
//    判断通道是否关闭
func testchan4() {
	ch1 := make(chan int)
	ch2 := make(chan int)
	//开启goroutine将0-100的数据发送到ch1
	go func() {
		for i := 0;i < 100 ; i++ {
			ch1 <- i
		}
		close(ch1)
	}()
	//开启goroutine从ch1中接受值，并将该值的平方发送到ch2中
	go func(){
		for {
			i,ok := <- ch1 // 通道关闭后 ok = false
			if !ok {
				break
			}
			ch2 <- i * i
		}
		close(ch2)
	}()
	//在主goroutine中从ch2中接收值并打印
	for i := range ch2 {
		fmt.Println(i)
	}
}
```

- 通道异常总结

![image-20200703102848725](/home/yex/.config/Typora/typora-user-images/image-20200703102848725.png)

- Close关闭通道注意点：
  1. 重复关闭channel会panic
  2. 向关闭的channel发数据会panic
  3. 从关闭的channel读数据不会panic，会读取出通道类型的默认值