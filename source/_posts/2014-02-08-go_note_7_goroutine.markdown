---
layout: post
title: "Go笔记7:并发"
date: 2014-02-08 21:06:19 +0800
comments: true
category: golang 
---
Golang 给人印象最深刻的一个特性就是：从语言层面支持并行，而且实现起来相当简单。

## goroutine
goroutine是Go并行设计的核心。goroutine其实就是协程，它比线程更小，十几个goroutine可能体现在底层就是五六个线程，Go语言内部实现了这些goroutine之间的内存共享。oroutine比thread更易用、更高效、更轻便。

goroutine通过`go`关键字实现：

```go  
go hello(a, b, c)  
```  
<!--more-->
## channels
说到并发，不得不说一下多线程之间的通讯（数据共享），说到这不由的会想起Java中的线程间通讯：通过同步和加锁在线程间传递数据，synchronized, locker, wait, notifyAll...光是想起来都让人头疼。但是Golang中，线程间的通讯实现来就要简单和直观的多，因为它提供了一个很好的通信机制channel。channel与Unix shell 中的双向管道很相似：可以通过它发送或者接收值。

使用make 创建channel(定义一个channel时，也需要定义发送到channel的值的类型)：

```go
ci := make(chan int)  
cs := make(chan string)  
cf := make(chan interface{})  
```

channel通过操作符<-来接收和发送数据:

```go
ch <- v    // 发送v到channel ch.  
v := <-ch  // 从ch中接收数据，并赋值给v  
```

默认情况下，channel接收和发送数据都是阻塞的，除非另一端已经准备好，这样就使得Goroutines同步变的更加的简单，而不需要显式的lock。所谓阻塞，也就是如果读取（value := <-ch）它将会被阻塞，直到有数据接收。其次，任何发送（ch<-5）将会被阻塞，直到数据被读出。无缓冲channel是在多个goroutine之间同步很棒的工具。

正式由于通过channel我们可以很简单地实现和控制Goroutines之间地同步，所以我们通常遵循一条设计原则：**不要通过共享来通信，而要通过通信来共享。**

## Buffered Channels
在Golang中实现带缓存地Channel非常简单，就是在申明Channel时指定缓存地大小即可，比如：

```go
ch:= make(chan bool, 4)   
```

创建了可以存储4个元素的bool 型channel。在这个channel 中，前4个元素可以无阻塞的写入。当写入第5个元素时，代码将会阻塞，直到其他goroutine从channel 中读取一些元素.

## Range和Close
Golang中非常贴心地提供可一个Range函数，方便我们通过轮询地方式读取Channel中地值，知道Channel被现实地关闭，避免了低效而且看上去比较愚蠢地一条一条地去读取，例如：

```go  
package main

import (
    "fmt"
)

func fibonacci(n int, c chan int) {
    x, y := 1, 1
    for i := 0; i < n; i++ {
        c <- x
        x, y = y, x + y
    }
    close(c)
}

func main() {
    c := make(chan int, 10)
    go fibonacci(cap(c), c)
    for i := range c {
        fmt.Println(i)
    }
}
```

`for i := range c`能够不断的读取channel里面的数据，直到该channel被显式的关闭。上面代码我们看到通过内置函数`close`显式关闭channel。关闭channel之后就无法再发送任何数据了，可以通过语法`v, ok := <-ch`测试channel是否被关闭。如果ok返回false，那么说明channel已经没有任何数据并且已经被关闭。

## Select
如果存在多个channel的时候，我们可以通过`select`监听channel上的数据流动。

`select`默认是阻塞的，只有当监听的channel中有发送或接收可以进行时才会运行，当多个channel都准备好的时候，select是随机的选择一个执行的。例如：

```go   
package main

import "fmt"

func fibonacci(c, quit chan int) {
    x, y := 1, 1
    for {
        select {
        case c <- x:
            x, y = y, x + y
        case <-quit:
            fmt.Println("quit")
            return
        }
    }
}

func main() {
    c := make(chan int)
    quit := make(chan int)
    go func() {
        for i := 0; i < 10; i++ {
            fmt.Println(<-c)
        }
        quit <- 0
    }()
    fibonacci(c, quit)
}
```

`select`里面还有default语法，`select`其实就是类似switch的功能，default就是当监听的channel都没有准备好的时候，默认执行的.

### 超时
我们可以利用select来设置超时，来避免整个程序进入阻塞的情况。例如：

```go  
func main() {
    c := make(chan int)
    o := make(chan bool)
    go func() {
        for {
            select {
                case v := <- c:
                    println(v)
                case <- time.After(5 * time.Second):
                    println("timeout")
                    o <- true
                    break
            }
        }
    }()
    <- o
}
```

## 参考资料

[Go Web 编程：并发](https://github.com/astaxie/build-web-application-with-golang/blob/master/ebook/02.7.md)