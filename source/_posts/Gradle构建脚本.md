---
title: Gradle构建脚本
date: 2019-10-17 10:47:40
keywords:
    - Gradle
tags:
    - Android
    - Gradle
categories:
    - Android
---

# 前言

Gradle提供了一种领域特定语言，目前同时支持 Groovy 和 Kotlin 。在 Groovy 构建脚本中（.gradle) 你可以使用任何 Groovy 元素。

在 Kotlin 构建脚本中 (.gradle.kts) 你可以使用任何 Kotlin 元素。

<!-- more -->

# 项目（Project）和任务（Task）

Gradle 构建的一切都是基于两个概念 ：项目和任务；

一个构建是由一个或多个项目组成的。

项目的概念比较抽象，你可以创建一个 Project 用于生成一个 jar,也可以定义个项目用于生成 war 包，还可以定义一个项目用于发布上传你的 war等。

一个项目就是在你的业务范围内，被你抽象出来的一个独立的模块，你可以根据工程的实际情况抽象归类，最后这一个个的项目组成了整个 Gradle 构建。

一个项目又包含很多个任务，每个项目是由一个或多个任务组成的。

任务就是一个操作，一个原子性的操作。比如打个 jar 包，复制一份文件，编译一次 java 代码等，这就是一个任务。

# build.gradle & Project API

每个项目都有一个 build.gradle 文件，该文件是该项目的构建入口，可以在这这个文件里对该项目进行配置，比如配置版本，需要哪些插件，依赖哪些库等。

我们通过配置这个文件描述我们的构建，这其实就是一个配置脚本。

每一个脚本在执行的时候都会被关联到一个 Project 实例上。

在构建生命周期的初始化阶段，Gradle 会为每个项目创建一个 Project 实例，并根据 build.gradle的内容配置这个实例。

也就是说每个 build.gradle 的配置都会被配置到 Project 实例上。

实际上，build.gradle 中几乎所有的顶级属性和代码块都是 Project 类的 API，

下面通过访问 Project.name 属性验证一下。

在 `build.gradle`

``` groovy
println "name is $name"
println "project.name is ${project.name}"
```

> 执行 build 任务，你将会得到下面的输出,输出的值都是 项目的名字

第一条语句使用的是Project的顶级属性。

第二条语句使用的 project 属性 可以在脚本的任何地方访问，它代表的是当前脚本的Project对象。

只有在你定义了和Project的成员（方法，属性）同名的时候才需要使用 project ，其他时候直接使用 名称即可访问，例如第一条语句。

**一个构建是由多个Project组成的，是通过项目树的形式表示的。**

可以在项目树的根项目对所有的项目统一配置一些配置。例如，应用的插件，依赖的 Maven 中心库等。

为所有子项目配置仓库为 jcenter

``` groovy
subprojects{
    repositories {
        jcenter()
    }
}
```

也可以为所有子项目配置 使用 Java 插件

``` groovy
subprojects{
    apply plugin:'java'
    repositories {
        jcenter()
    }
}
```

除了 subprojects 还有 allprojects ,从名字就可以看出来这不仅是对子项目的配置而是对所有项目的配置。

这两个配置其实是两个方法，接受一个闭包参数，对项目进行遍历，遍历的过程中调用我们自定义的闭包，所以我们可以在闭包里配置，打印，输出或者修改 Project 的属性。

# Project的属性

Project 对象的属性在 脚本全局都是可以使用的。

下面列出一些常用的属性，更全的属性可以在 Project API 中查询。

| 名字        | 类型       | 默认值                    |
| ----------- | ---------- | ------------------------- |
| project     | Project    | Project 实例              |
| name        | String     | 项目名字                  |
| path        | String     | 项目的绝对路径            |
| description | String     | 项目描述                  |
| projectDir  | File       | 配置脚本所在的目录        |
| buildDir    | File       | projectDir/build 输出目录 |
| group       | Object     | 未指定                    |
| version     | Object     | 未指定                    |
| ant         | AntBuilder | AntBuilder 实例           |

# settings.gradle

这个文件是由 Gradle 约定命名的，默认名为 settings.gradle ，在初始化阶段被执行。

对于多项目构建，必须在这里声明要参与构建的所有项目。对于单项目构建就是可选的了，可有可无。

Gradle 是如何寻找 settings.gradle 的？

1.在当前目录寻找2.没有找到的话就去父目录寻找3.仍然没有找到就是是单项目构建了4.如果找到了就是确定其中的项目，如果当前执行的项目在 settings.gradle 有定义就执行多项目构建，否则就执行单项目构建。

一个脚本的属性访问和方法调用是委托给 Project 类的实例的，

类似的 settings.gradle 的属性访问和方法调用是委托给 Settings 类的实例对象的。

# script API

当 Gradle 执行 Groovy 脚本(.gradle)时，会编译脚本到实现了 `Script` 的类中。也就是说，Script 接口中的所有属性和方法都可以在脚本中使用。

当 Gradle 执行 Kotlin 脚本(.gradle.kts)时,会编译脚本到 `KotlinBuildScript`的子类中。

也就是说 KotlinBuildScript 类中的所有属性和方法都可以在脚本中使用。

更详细的可以参考 KotlinSettingsScript 和 KotlinInitScript 类，分别用于设置脚本和init脚本。

写的确实是脚本，但不是简单的脚本。在脚本里可以定义 Class ，内部类，导入包，定义方法、常量、接口等。

不要把它当作简单的脚本，我们可以灵活的使用 Java ，Groovy ，Kotlin 和 Gradle.

例如 定义一个获取当前日期的方法

``` groovy
def buildTime(){
     def date = new Date()
     def formattedDate = date.format('yyyyMMdd')
     return formattedDate
 }
```

# 变量 & 额外的自定义属性

Gradle 支持两种变量 ：

- 局部变量
- 自定义属性

## 局部变量

局部变量使用 def 关键字声明，局部变量只能在声明的范围内可见。

``` groovy
def myName = 'local var'
```

# 额外的自定义属性

Gradle 领域模型中 所有的对象 都可以添加额外的自定义属性。

通过对象的 ext 属性实现对自定义属性的添加，访问，设置值的操作。

添加之后可以通过 ext 属性对自定义属性读取和设置，也可以同时添加多个自定义属性。

``` groovy
//为 project 添加一个 age 属性 并赋值 20
 ext.age = 20

 //为 project 添加两个属性
 ext{
     phone =110
     address = '404'
 }

 task myTask {
     //为 myTask 任务添加属性
     ext.myProperty = "myValue"
 }
 task extra{
      doLast{
          println "project : age= ${project.ext.age},phone= ${project.ext.phone} , address = ${project.ext.address}"
          println "myTask :  ${myTask.myProperty}"
      }

 }
```

# 创建一个任务

``` groovy
task hello {
     doLast {
         println 'Hello world!'
     }
 }
```

这里的 task 看着像一个关键字，实际上是一个方法，这个方法的原型是 TaskContainer.create(）

任务的创建就是使用这个方法给 Project 添加一个 Task 类型的属性；

所以才能使用任务名字引用一些API，例如为任务添加额外的属性。

# 任务依赖和任务排序

一个任务可以依赖其他任务或者在其他任务执行后再执行。

Gradle 确保在执行任务时遵守所有任务依赖性和排序规则，以便在所有依赖项和任何 “必须运行” 的任务执行之后再执行任务。

Gradle 为我们提供了几个方法用来控制任务的依赖和排序，就是下面这几个

- Task.dependsOn(java.lang.Object[])
- Task.setDependsOn(java.lang.Iterable)
- Task.mustRunAfter(java.lang.Object[])
- Task.setMustRunAfter(java.lang.Iterable)
- Task.shouldRunAfter(java.lang.Object[])
- Task.setShouldRunAfter(java.lang.Iterable)

这些方法可以接收 任务，任务名字，路径等，具体参数可以在 [Task文档](https://docs.gradle.org/current/userguide/more_about_tasks.html) 里查看

``` groovy
task hello {
     doLast {
         println 'Hello world!'
     }
 }

 task taskX {
     dependsOn hello
     doLast{
         println "I'm  $name."
     }
 }

 task taskY {
     doFirst {
         println "I'm $name."
     }
 }
```

如果 taskX 要依赖 taskY 的话，并不能直接引用，因为 taskY 是在 taskX 之后定义的。

``` groovy
task taskX {
     dependsOn 'taskY'
     doLast{
         println "I'm  $name."
     }
 }
```

# 默认任务

在没有指定执行任务的时候，可以在脚本中定义默认任务，使用 defaultTasks 方法

这个方法接收 字符串参数，传入任务的名称即可

``` groovy
defaultTasks 'hello','taskY'
```

# 外部依赖

用添加外部依赖，必须添加依赖所在仓库。例如 jcenter,maven，google等

添加 google 仓库

``` groovy
allprojects {
    repositories {
        mavenCentral()
        google()
        jcenter()
    }
}
```

在 Android 的项目中，这都是放在根项目里的 allprojects 方法里，对所有项目统一配置

添加外部依赖

``` groovy
dependencies {
    implementation 'io.reactivex.rxjava2:rxjava:2.1.2'
}
```

在 Android 中依赖的添加放在了各个module 中，按需添加，哪个模块需要在哪个模块的构建脚本里添加。

依赖属性分为三部分

- group：这个属性用来标识一个组织、公司或者项目，可以用点号分隔，例如上面的 io.reactivex.rxjava2
- name：name属性唯一的描述了这个依赖，例如上面的 rxjava
- version：一个库可以有很多个版本。例如上面的 2.1.2

其中 implementation 为配置项，配置也有很多种类型，

| 新配置(3.0后)    | 弃用配置(3.0前) | 说明                                                         | 作用                                                         |
| ---------------- | --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `implementation` | `compile`       | 依赖项在编译时对模块可用，并且仅在运行时对模块的消费者可用。 对于大型多项目构建，使用`implementation`而不是`api/compile`可以显著缩短构建时间，因为它可以减少构建系统需要重新编译的项目量。 大多数应用和测试模块都应使用此配置。 | 使用`implementation`方式来依赖项目或库，该库在编译时，只对当前的module可见，对其他的module不可见。 |
| `api`            | `compile`       | 依赖项在编译时对模块可用，并且在编译时和运行时还对模块的消费者可用。 此配置的行为类似于`compile`（现在已弃用），一般情况下，您应当仅在库模块中使用它。 应用模块应使用`implementation`，除非您想要将其 API 公开给单独的测试模块。 | 使用`api`方式来依赖项目或库，该库在编译和运行时都可以对其他module可见。 |
| `compileOnly`    | `provided`      | 依赖项仅在编译时对模块可用，并且在编译或运行时对其消费者不可用。 此配置的行为类似于`provided`（现在已弃用）。 | 使用`compileOnly`方式来依赖项目或库，该库仅在编译时有效可用。 |
| `runtimeOnly`    | `apk`           | 依赖项仅在运行时对模块及其消费者可用。 此配置的行为类似于`apk`（现在已弃用）。 | 使用`runtimeOnly`方式来依赖项目或库，该库仅在运行时有效可用。 |

> 最后留个 DSL 的查询地址：https://docs.gradle.org/current/dsl/index.html

# 参考资料

https://www.cnblogs.com/skymxc/p/buildscript.html

https://www.jianshu.com/p/d183c3b554e5

https://docs.gradle.org/

