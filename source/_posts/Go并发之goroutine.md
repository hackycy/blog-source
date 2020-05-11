---
title: Go并发之goroutine
date: 2018-09-24 22:05:45
tags:
    - Golang基础
categories:
    - Golang
---

# Go语言并发优势


作为云计算时代的C语言之称的Go语言，第一是因为Go语言设计简单，第二21实际最重要的就是并发程序设计，而Go在语言层面上就支持了并发。在此同时，并发程序的内存管理有时候是非常复杂的，而Go自身也提供了自动垃圾回收机制。

<!-- more -->

Go语言为并发编程而内置的上层API是基于CSP模型，这里引用了知乎[原文链接](https://www.zhihu.com/question/26192499),CSP(Communicating Sequential Process)模型和Actor模型是两门非常复古且外形接近的并发模型。但CSP与Actor有以下几点比较大的区别：

- CSP并不Focus发送消息的实体/Task，而是关注发送消息时消息所使用的载体，即channel。
- 在Actor的设计中，Actor与信箱是耦合的，而在CSP中channel是作为first-class独立存在的。
- 另外一点在于，Actor中有明确的send/receive的关系，而channel中并不区分这样的关系，执行块可以任意选择发送或者取出消息。

另外默认情况下的channel是无缓存的,对channel的send动作是同步阻塞的，直到另外一个持有该channel引用的执行块取出消息(channel为空)，反之，receive动作亦然。藉此，我们可以得到一个基本确定的事实，by default时，实际的receive操作只会在send之后才被发生。而Actor中，由于send这个动作是异步的，因此Actor的receive会按照信箱接受到消息的顺序来进行处理。当然，除此以外，channel还有种Buffered Channel的模式，在默认情况的基础上，你可以确定channel内的消息数量，当channel中消息数量不满足于初始化时Buffer数目时，send动作不会被阻塞，写入操作会立即完成(因此Buffered Channel在很大程度上与Actor非常接近)，直到Buffer数目已满，则send动作开始阻塞。

这也意味着显示锁都是可以避免的，因为Go语言通过安全的通道发送和接受以实现同步，也简化了并发程序的编写。

而一般情况下，桌面计算机跑十几二十个线程就有点负载了，而同样的一台计算机，却可以轻松跑
起成百上千甚至万个goroutine进行资源竞争。


# goroutine是什么

goroutine是Go并行设计的设置核心，它其实就是协程，但是它比线程更小，十几个goroutine可能体现在底层就是五六个线程，Go语言内部帮你实现了这些goroutine之间的内存共享。执行goroutine只需极少的栈内存，当然会根据相应的数据伸缩。也正是如此，可以同时运行成千上万个并发任务。goroutine比thread更易用、更高效、更轻便。

# goroutine的创建

只需要函数调用前加一个 __ go __ 关键字，就可以创建并发执行单元。无需了解其他细节，调度器会自动安排到合适的系统线程上执行。

像java语言一样，go也有其main函数，有着其main thread。在go语言中，main函数既在一个单独的goroutine中运行，也可以成为main goroutine。新的goroutine用go语句来创建。

示例代码1：
``` Go
package main

import (
    "fmt"
    "time"
)

func main() {
    for { //死循环
        fmt.Println("this is main goroutine")
        time.Sleep(time.Second)
    }
}
```
运行结果：
![](main_goroutine.png)

手动退出，这是一个主协程，那么再看下面一个代码
示例代码2：
``` Go
package main

import (
    "fmt"
    "time"
)

func newTask(){
    for {
        fmt.Println("this is a new Task")
        time.Sleep(time.Second)
    }
}

func main() {

    for {
        fmt.Println("this is main goroutine")
        time.Sleep(time.Second)
    }

    newTask()
}
```
运行结果：
![](main_goroutine.png)

毫无疑问示例2与示例1中运行结果没有区别，而如何让他们同时执行呢

示例代码3：
``` Go
package main

import (
    "fmt"
    "time"
)

func newTask(){
    for {
        fmt.Println("this is a new Task")
        time.Sleep(time.Second)
    }
}

func main() {

    go newTask() //新建一个协程
    //需要将代码放置前面，如果放置后面，死循环同样没办法执行这条语句

    for {
        fmt.Println("this is main goroutine")
        time.Sleep(time.Second)
    }

}
```

运行结果：
![](new_goroutine.png)

一个新的协程只需要一个关键字go就已经完成了。

# main goroutine先退出

我们再看一个奇怪的现象

示例代码4：
``` Go
package main

import (
    "fmt"
    "time"
)

func newTask(){
    for {
        fmt.Println("this is a new Task")
        time.Sleep(time.Second)
    }
}

func main() {

    go newTask()

}
```
运行结果：
![](main_goroutine_exit_first.png)

为什么结果什么都没有，我们明明已经开了一个新的协程来进行执行newTask函数，这就是go中main函数执行的太快了，也就是main goroutine已经执行完了，所有的goroutine都会直接打断，程序退出。

为了防止main goroutine过快的结束，
``` Go
package main

import (
    "fmt"
    "time"
)

func newTask(){
    for {
        fmt.Println("this is a new Task")
        time.Sleep(time.Second)
    }
}

func main() {

    go newTask()

    time.Sleep(5 * time.Second)

}
```
运行结果：
![](sleep_main.png)

让main goroutine睡了5秒，而newTask也刚好执行了5次，也解释了当main goroutine结束时，所有的goroutine都会直接打断，程序退出。