---
title: 单例模式-JAVA实现
date: 2018-11-20 14:27:22
tags:
    - 设计模式
    - JAVA
categories:
    - 设计模式
---

# 单例模式

什么是单例？就是保证在一个jvm当中只能由一个实例。（区分不是在多个jvm当中。）

单例模式有七种写法。

本文只提出两种，一种懒汉式和饿汉式。

<!-- more -->

## 懒汉式

``` Java
public class Single {

    public static void main(String[] args) {
        System.out.println(Singleton.getInstance());
        System.out.println(Singleton.getInstance());
        System.out.println(Singleton.getInstance());
        System.out.println(Singleton.getInstance());
    }

}

class Singleton {

    private static Singleton mSingleton;

    private Singleton(){}

    public static Singleton getInstance() {
        if (mSingleton == null) {
            synchronized (Singleton.class) { //加锁，线程不安全，故加上
                mSingleton = new Singleton();
            }
        }
        return mSingleton;
    }

}
```

``` bash 
cn.net.sweetlover.patern.Singleton@1540e19d
cn.net.sweetlover.patern.Singleton@1540e19d
cn.net.sweetlover.patern.Singleton@1540e19d
cn.net.sweetlover.patern.Singleton@1540e19d
```

被需要时才进行初始化，且只有同一个实例，但会发生线程不安全。

## 饿汉式

``` Java
public class Single {

    public static void main(String[] args) {
        System.out.println(Singleton.getInstance());
        System.out.println(Singleton.getInstance());
        System.out.println(Singleton.getInstance());
        System.out.println(Singleton.getInstance());
    }

}

class Singleton {

    private final static Singleton mSingleton = new Singleton();

    private Singleton(){}

    public static Singleton getInstance() {
        return mSingleton;
    }

}
```

``` bash 
cn.net.sweetlover.patern.Singleton@1540e19d
cn.net.sweetlover.patern.Singleton@1540e19d
cn.net.sweetlover.patern.Singleton@1540e19d
cn.net.sweetlover.patern.Singleton@1540e19d
```

> 这两者的区分：懒汉式不是线程安全的，且效率比饿汉式要低（因为加了同步），但是节约内存
> 。但饿汉式是天生线程安全的，因为当class被加载的时候就已经被初始化了。