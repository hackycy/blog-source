---
title: Go并发之channel
date: 2018-09-25 10:38:59
tags:
    - Golang基础
categories:
    - Golang
---

# channel是什么

channel是Go中一个非常重要的特性，在Go并发之goroutine里有介绍了CSP模型，CSP 是 Communicating Sequential Process 的简称，中文可以叫做通信顺序进程，是一种并发编程模型。Go实现了CSP部分的理论，goroutine对应CSP中并发执行的实体，channel也就对应着CSP中的channel。

<!-- more -->

# channel操作符

操作符 `<-`

``` Go
ch := make(chan int)

//读
x <- chan

//写
chan <- 10
```

# channel创建

必须使用make创建channel

``` Go
bufChan := make(chan int, 10) //1、带缓冲channel
noBufChan := make(chan string) //2、无缓冲channel
```

和map类似，channel也是一个对应make创建的底层数据结构的引用，当我们复制一个channel或者用于函数参数传递时，只是拷贝了一个channel的引用，channel的零值为nil。

以方式1创建的channel是带缓冲的，方式2即为无缓冲的。

示例代码1：
``` Go
package main

import "fmt"

func main() {
    channel := make(chan int) //创建一个int类型的channel

    //匿名函数
    go func() {
        channel <- 10 
        fmt.Println("chan <-") //子协程
    }()

    <- channel //没有数据前阻塞
    fmt.Println("<- chan")
}
```
运行结果：
![](channel_example1.png)

再来看一下下面这个例子

``` Go
package main

import "fmt"

func main() {
    var channel chan int

    go func() {
        channel <- 10
        fmt.Println("chan <-") 
    }()

    <- channel 
    fmt.Println("<- chan")

}
```
运行结果：
![](no_init_chan_deadlock.png)

为什么会deadlock，因为channel并没有创建。

# 不带缓冲channel

一个基于无缓存的channel的发送操作将导致发送者goroutine阻塞，直到另一个goroutine在相同的channel上执行接收操作，当发送的值通过channel成功传输之后，两个goroutine可以继续执行后面的语句。反之，如果接受操作先发生，那么接收者goroutine也将阻塞，直到有另一个goroutine在相同的channel上执行发送操作。

基于无缓存channel的发送和接收操作将导致两个goroutine做一次同步操作。因为这个原因，无缓存channel有时候也被成为同步channel。当通过一个无缓存channel发送数据时，接收者收到数据发生在唤醒发送者goroutine之前。

channel接受和发送数据都是阻塞的，除非另一端已经准备好，这样使得goroutine同步变得更加的简单，不用显式的lock。 
** 谓阻塞就是读取，会被阻塞，直到有数据接收。其次，任何发送将会被阻塞，直到数据被读出。 ** 无缓冲的channel是多个goroutine之间同步很棒的工具。

示例代码：
``` Go
package main

import "fmt"

func sum(a []int, ch chan int){
    total := 0
    fmt.Println(a)
    for _,v := range a {
        total += v
    }
    ch<- total
}

func main() {
    ch := make(chan int)
    a := []int{1,2,3,4,5,6}
    go sum(a[:3], ch)
    go sum(a[3:], ch)
    x := <-ch
    y := <-ch
    fmt.Println(x, y)
}
```

运行结果：
![](channel_example2.png)

结果有两种，这只是其中一种。

# 带缓冲的channel

在Go中也允许指定channel的缓冲大小

``` Go
ch := make(chan type, value)
```

当 value = 0 时，channel就是无缓冲的channel，当 value > 0 时， channel有缓冲、是非阻塞的，直到写满value个元素后才阻塞写入。

示例代码：
``` Go
package main

import "fmt"

func main() {
    ch := make(chan int, 2) //缓冲大小为2
    ch<- 10
    ch<- 20

    fmt.Println(<-ch)
    fmt.Println(<-ch)
}
```
运行结果：
![](channel_example3.png)

如果代码中将缓存区域设置为1，则会出现deadlock错误。
fatal error: all goroutines are asleep - deadlock!

# 单向channel

默认情况下是双向的，即可读可写，读代表receive，写代表send。

通常情况我们会将一个通道作为函数参数传递过去，但是默认是双向的，我只想让该参数只能进行读或者写，那就用到了单向channel。

``` Go
var ch1 chan int //默认双向
var ch2 chan<- int //单向，只读
var ch3 <-chan int //单项，只写
```

双向channel可以隐式转换为单向channel，反之则不行。

# 关闭channel

关闭channel只需要close函数即可，可以使用多返回值的方式 `v,ok := <-ch`方式来判断channel是否被关闭。

示例代码：
``` Go
package main

import "fmt"

func foreach(arr []int, b chan bool){
    for i,v := range arr {
        fmt.Printf("i=%d, v=%d \n", i, v)
    }
    b<- true
    close(b)
}

func main() {
    arr := []int{12,4,6,7,9,2}
    ch := make(chan bool, 10)
    go foreach(arr, ch)
    if v, ok := <-ch; !ok {
        fmt.Println(v)
    }
}
```

当一个channel被关闭后，再向该channel发送数据将导致panic异常。

其实你并不需要关闭每一个channel。只要当需要告诉接收者goroutine，所有的数据已经全部发送时才需要关闭channel。不管一个channel是否被关闭，当它没有被引用时将会被Go语言的垃圾自动回收器回收。（不要将关闭一个打开文件的操作和关闭一个channel操作混淆。对于每个打开的文件，都需要在不使用的使用调用对应的Close方法来关闭文件。）
试图重复关闭一个channel将导致panic异常，试图关闭一个nil值的channel也将导致panic异常。关闭一个channels还会触发一个广播机制

# Range遍历channel

可以通过range，像操作slice或者map一样操作缓存类型的channel

使用range实现斐波那契数列

示例代码：
``` Go
package main

import "fmt"

func fibonacci(n int, f chan int){
    x, y := 1, 1
    for i:=0; i<n; i++ {
        f <- x
        x, y = y, x+y
    }
    close(f)
}

func main() {
    ch := make(chan int, 10)
    go fibonacci(cap(ch), ch)
    for v := range ch {
        fmt.Printf("%d  ", v)
    }
}

```

# Select

Go提供了一个关键字select，通过select可以监听channel上的数据流动。

语法和恶switch非常相似，每个条件由case来描述。但是select有很多限制，其中一条就是每个case语句里必须是一个IO操作。

大致结构
``` Go
selct {
    case <-ch:
    //如果读数据，则执行
    case ch<- 1:
    //如果写数据，则执行
    default:
    //默认处理流程，但是一般情况select不用该条件，因为无意义
}
```

在一个select语句中，Go会按顺序从头到尾评估每一条发送和接收的语句。

但是其中任意一语句都可以，是任意都可以(即没有被堵塞)，那么就从那么可以执行的语句中任意选择来使用。

如果没有任意一条语句可以执行，那么可能有两种情况。
- 如果给出了default语句，则会执行default语句，同时程序的执行会从select语句后的语句中恢复。
- 如果没有default语句，那么select语句将被阻塞，直到有一个通信可以进行下去。

使用select实现斐波那契数列
示例代码：
``` Go
package main

import "fmt"

func fibonacci(n chan int, b chan bool) {
    x, y := 1, 1
    for {
        select {
        case n<- x:
            x, y = y, x+y
        case <-b:
            fmt.Println("end")
            return
        }
    }
}

//使用select实现斐波那契
func main() {
    n := make(chan int, 10)
    b := make(chan bool)

    go func() {

        for i := 0; i < cap(n); i++ {
            fmt.Println(<-n)
        }

        b<- true

    }()

    fibonacci(n, b)
}

```
运行结果：
![](select_feibo.png)

