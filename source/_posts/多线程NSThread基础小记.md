---
title: 多线程NSThread基础小记
date: 2020-12-15 14:17:52
tags:
    - Ios
categories:
    - Ios
---

# 前言

NSThread 基于OC的API,使用其简单，面向对象操作。但线程周期由程序员管理。

**优点：**轻量级
**缺点：**需要自己管理线程的生命周期，线程同步。线程同步对数据的加锁会有一定的系统开销

**苹果推荐是用GCD 和 NSOperation**

<!-- more -->

> 注意：
>  [NSThread currentThread]跟踪任务所在线程，适用于NSThread、NSOperation、GCD
>  使用NSThread的线程，不会自动添加autoreleasepool
>  线程中的自动释放池：
>  @autoreleasepool{}自动释放池。主线程中是有自动释放池，使用NSThread 和 NSObject 不会有。如果在后台线程中创建了autoreleasepool的对象，需要使用自动释放池，否则会出现内存泄漏。当自动释放池销毁时，对池中的所有对象发送release消息，清空自动释放池。当所有的autorelease对象，在出了作用域后，会自动添加到最近一次创建的自动释放池中。

# NSThread 常用属性

```objc
@property (nullable, copy) NSString *name  //线程名字
@property NSUInteger stackSize  //线程栈大小,默认主线程1m ,子线程512k,次属性可读写,但是写入大小必须为4k的倍数,最小为16k
@property (readonly) BOOL isMainThread // 是否是主线程
@property (readonly, getter=isExecuting) BOOL executing  //是否正在执行
@property (readonly, getter=isFinished) BOOL finished  //是否已经完成
@property (readonly, getter=isCancelled) BOOL cancelled  //是否已经取消
```

# NSThread类方法 作用于当前线程

```objc
+ (void)detachNewThreadSelector:(SEL)selector toTarget:(id)target withObject:(nullable id)argument; //开辟一个新的线程
+ (void)sleepUntilDate:(NSDate *)date;//休眠到什么时候（具体日期）
+ (void)sleepForTimeInterval:(NSTimeInterval)ti; //休眠一段时间单位秒
+ (void)exit; //结束当前线程
+ (double)threadPriority; //返回当前线程优先级
+ (BOOL)setThreadPriority:(double)p; //设置当前线程优先级 0.0~1.0
+ (NSArray<NSNumber *> *)callStackReturnAddresses //返回当前线程访问的堆栈信息
+ (NSArray<NSString *> *)callStackSymbols //返回一堆十六进制的地址
+ (BOOL)isMainThread //返回当前线程是否是主线程
+ (NSThread *)mainThread //返回主线程
```

# NSThread实例方法

```objc
- (instancetype)init
- (instancetype)initWithTarget:(id)target selector:(SEL)selector object:(nullable id)argument 
- (void)main    //主方法，用于子类继承重写
- (void)start   //开始线程
- (void)cancel  //取消线程
```

# NSThread 详解

## 线程的生命周期

- 创建线程的方法

```objc
 - (instancetype)initWithTarget:(id)target selector:(SEL)selector object:(id)argument //此方法创建的线程需要手动启动
 + (void)detachNewThreadSelector:(SEL)selector toTarget:(id)target withObject:(id)argument//创建线程后自动启动
 - (void)performSelectorInBackground:(SEL)aSelector withObject:(id)arg//隐式创建线程并启动
```

- 配置线程

```objc
//通过name属性设置线程名字
+ (BOOL)setThreadPriority:(double)p设置线程的优先级
```

- 启动线程

```objc
 - (void)start
```

- 阻塞线程

```objc
+ (void)sleepUntilDate:(NSDate *)date
+ (void)sleepForTimeInterval:(NSTimeInterval)time
```

- 取消线程

```objc
- (void)cancel //当前正在执行的线程不会立刻停止
```

- 强制退出线程

```objc
+ (void)exit
```

## NSThread的其他操作

- 与主线程相关

```objc
+ (NSThread *)mainThread //获取主线程
+ (BOOL)isMainThread//判断当前线程是不是主线程
```

- 与当前线程相关

```objc
+ (NSThread *)currentThread //获取当前线程
```

- 判断线程的状态

```objc
* 通过executing属性判断线程是否正在执行
* 通过finished属性判断线程是否执行完毕
* 通过cancelled属性判断线程是否被取消
```

- 线程同步
- 原因：多个线程访问同一资源，很可能会引起数据错乱和数据安全问题
- 解决方案：使用互斥锁来解决互斥资源访问问题，iOS中通常使用@synchronized(锁){}对临界资源进行锁定，通常使用self作为锁
- 注意：由于线程同步会消耗大量的资源，应尽量避免多个线程访问同一资源，且通常将线程同步的逻辑交由服务器端实现

## 线程之间的通信

- 从子线程回到主线程

```objc
- (void)performSelectorOnMainThread:(SEL)aSelector withObject:(id)arg waitUntilDone:(BOOL)wait modes:(NSArray *)array
//array指定runLoop的模式，若为空，则不执行aselector方法的调用者即为aselector的调用者
- (void)performSelectorOnMainThread:(SEL)aSelector withObject:(id)arg waitUntilDone:(BOOL)wait
```

- 从一个线程到另一个线程(包括主线程)

```objc
- (void)performSelector:(SEL)aSelector onThread:(NSThread )thr withObject:(id)arg waitUntilDone:(BOOL)wait modes:(NSArray )array
- (void)performSelector:(SEL)aSelector onThread:(NSThread *)thr withObject:(id)arg waitUntilDone:(BOOL)wait
```

**也可以通过NSPort对象实现通信**

# 反思

## 资源共享

- 1块资源可能会被多个线程共享，也就是多个线程可能会访问同一块资源
- 当多个线程访问同一块资源时，很容易引发数据错乱和数据安全问题

## 解决办法互斥锁

- 互斥锁使用格式

> 1.@synchronized(锁对象) { // 需要锁定的代码 }
> 2.只用一把锁,多锁是无效的

- 互斥锁的优缺点

> 优点：能有效防止因多线程抢夺资源造成的数据安全问题
> 缺点：需要消耗大量的CPU资源

- 互斥锁的使用前提：多条线程抢夺同一块资源

# 参考资料

摘抄自 https://www.jianshu.com/p/8c7635428599