---
title: Android中MVC、MVP、MVVM介绍
date: 2019-04-01 22:19:32
keywords:
    - Android
    - MVC
    - MVP
    - MVVM 
tags:
    - Android
categories:
    - Android
---

**MVC,MVP,MVVM简单介绍**

<!-- more -->

# MVC

`MVC`全名是`Model-View-Controller`，是模型(model)-视图(view)-控制器(controller)的缩写，一种软件设计的典范，用一种业务逻辑、数据、界面显示分离的方式来组织代码，在改进和个性化定制洁面以及用户交互的同时，不需要重新编写业务逻辑。其中`Model`层处理数据，业务逻辑等。`View`层处理界面的显示结果。`Controller`层起到桥梁的作用，来控制`View`层和`Model`层通信以此来达到分离视图显示和业务逻辑层。

![](mvc.png)

我们往往把Android中界面部分的实现也理解为采用了MVC框架，常常把Activity理解为MVC模式中的Controller。

**看似没有什么特别的地方，但是由几个需要特别关注的关键点：**

- View是把控制权交给Controller，自己不执行业务逻辑。
- Controller执行业务逻辑并且操作Model，但不会直接操作View，可以说它是对View无知的。
- View和Model的同步消息是通过观察着模式进行，而同步操作是由View自己请求Model的数据然后对视图进行更新。

## MVC的优缺点

**优点**

- 把业务逻辑全部分离到controller中，模块化程度高。当业务逻辑变更的时候，不需要更改view和model，只需要更改controller即可（swappable controller）。
- 观察者模式可以做到多视图同时更新

**缺点**

- controller测试困难，因为视图同步操作是由view自己执行，而view只能在有UI的环境下运行。在没有UI的环境下对controller进行单元测试的时候，controller业务逻辑的正确性是无法验证的。controller更新model的时候，无法对view的更新操作进行断言。
- view无法组件化。view是强依赖特定的model的，如果需要把这个view抽出来作为另外一个应用程序可服用的组件就困难了，因为不同的程序model是不一样的。

# MVP

`MVP`其实是`MVC`的一种演进版本，它更简单，将`MVC`中的`Controller`改为了`Presenter`，`View`通过接口与`Presenter`进行交互，降低耦合，方便进行单元测试。

- **View:**负责绘制UI元素，与用户进行交互（Activity、View、Fragment都可以作为View层）
- **Model:**对数据的操作、对网络等的操作，和业务相关的逻辑处理
- **Presenter:**作为View与Model交互的中间纽带，处理与用户交互的逻辑。可以把Presenter理解为一个中间层的角色，它接受Model层的数据，并且处理之后传递给View层，还需要处理View层的用户
交互等操作。

![](mvp.png)

**关键点**

- View不再负责同步的逻辑，而是由Presenter负责。Presenter中既有业务逻辑也有同步逻辑。
- View需要提供操作界面的接口给Presenter进行调用。（关键）

对比在MVC中，Controller是不能操作View的，View也没有提供相应的接口。而在MVP当中，Presenter可以操作View，View需要提供一组对界面操作的接口给Presenter进行调用。Model仍然通过事件广播自己的变更，但由于Presenter监听而不是View。

## MVP（Passive View）优缺点

**优点**

- 便于测试，Presenter对View是通过接口进行，在对Presenter进行不依赖UI环境的单元测试的时候，可以通过Mock一个View对象，这个对象只需要实现了View的接口即可。然后依赖注入到Presenter中，单元测试的时候就可以完整的测试Presenter业务逻辑的正确性。
- View可以进行组件化。在MVP当中，View不依赖Model。这样就可以让View从特定的业务场景中脱离出来，可以说View可以做到对业务逻辑完全无知。它只需要提供一系列接口提供给上层接操作。这样就可以做到高度可复用的View组件。

**缺点**

- Presenter中除了业务逻辑意外，还有大量的View->Model，Model->View的手动同步逻辑，造成Presenter比较笨重，维护起来会比较困难。

# MVVM

`MVVM`模式（Mode-View-ViewModel），和`MVP`模式相比，`MVVM`模式用ViewModel替换了Presenter，其他层基本上与`MVP`模式一直，`ViewModel`可以理解成是`View`的数据模型和`Presenter`的合体。

`MVVM`采用双向绑定（data-binding）。View的变动，自动反应在ViewModel，反之亦然。这种模式实际上是框架替应用开发者做了一些工作（相当于ViewModel类是由库帮我们生成的），开发者只需要较少代码就能实现比较复杂的交互。

![](mvvm.png)

**MVVM调用关系**

MVVM的调用关系和MVP一样。但是，在ViewModel当中会有一个叫Binder，或者是Data-binding engine的东西。以前全部由Presenter负责的View和Model之间数据同步操作交由给Binder处理。你只需要在View的模版语法当中，指令式地声明View上的显示的内容是和Model的哪一块数据绑定的。当ViewModel对进行Model更新的时候，Binder会自动把数据更新到View上去，当用户对View进行操作（例如表单输入），Binder也会自动把数据更新到Model上去。这种方式称为：Two-way data-binding，双向数据绑定。可以简单而不恰当地理解为一个模版引擎，但是会根据数据变更实时渲染。

**关键点**

- MVVM把View和Model的同步逻辑自动化了。以前Presenter负责的View和Model同步不再手动地进行操作，而是交由框架所提供的Binder进行负责。
- 只需要告诉Binder，View显示的数据对应的是Model哪一部分即可。

## MVVM优缺点

**优点**

- 提高可维护性。解决了MVP大量的手动View和Model同步的问题，提供双向绑定机制。提高了代码的可维护性。
- 简化测试。因为同步逻辑是交由Binder做的，View跟着Model同时变更，所以只需要保证Model的正确性，View就正确。大大减少了对View同步更新的测试。

**缺点**

- 过于简单的图形界面不适用，或说牛刀杀鸡。
- 对于大型的图形应用程序，视图状态较多，ViewModel的构建和维护的成本都会比较高。
- 数据绑定的声明是指令式地写在View的模版当中的，这些内容是没办法去打断点debug的。

# 总结

三者，都是后者解决前者遗留的问题，但每个存在必有其的理由。每种模式并不能因为用而用，而是根据业务的需求来定制。
