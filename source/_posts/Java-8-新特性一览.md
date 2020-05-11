---
title: Java 8 新特性一览
date: 2020-01-04 10:13:58
tags:
    - Java
categories:
    - Java
---

Java 8 (又称为 jdk 1.8) 是 Java 语言开发的一个主要版本。 Oracle 公司于 2014 年 3 月 18 日发布 Java 8 ，它支持函数式编程，新的 JavaScript 引擎，新的日期 API，新的Stream API 等。

<!-- more -->

# 新特性

Java8 新增了非常多的特性，我们主要讨论以下几个：

- **Lambda 表达式** − Lambda 允许把函数作为一个方法的参数（函数作为参数传递到方法中）。
- **方法引用** − 方法引用提供了非常有用的语法，可以直接引用已有Java类或对象（实例）的方法或构造器。与lambda联合使用，方法引用可以使语言的构造更紧凑简洁，减少冗余代码。
- **默认方法** − 默认方法就是一个在接口里面有了一个实现的方法。
- **Stream API** −新添加的Stream API（java.util.stream） 把真正的函数式编程风格引入到Java中。
- **Date Time API** − 加强对日期与时间的处理。
- **Optional 类** − Optional 类已经成为 Java 8 类库的一部分，用来解决空指针异常。

> 更多相关请浏览->https://www.oracle.com/technetwork/java/javase/8-whats-new-2157071.html

# Lambda 表达式

Lambda 表达式，也可称为闭包，它是推动 Java 8 发布的最重要新特性。

Lambda 允许把函数作为一个方法的参数（函数作为参数传递进方法中）。

使用 Lambda 表达式可以使代码变的更加简洁紧凑。

## 语法

lambda 表达式的语法格式如下：

``` java
(parameters) -> expression 
// or
(parameters) ->{ statements; }
```

- **可选类型声明：**不需要声明参数类型，编译器可以统一识别参数值。
- **可选的参数圆括号：**一个参数无需定义圆括号，但多个参数需要定义圆括号。
- **可选的大括号：**如果主体包含了一个语句，就不需要使用大括号。
- **可选的返回关键字：**如果主体只有一个表达式返回值则编译器会自动返回值，大括号需要指定明表达式返回了一个数值。

来看一下最简单的使用

``` java
public class Lambda {

    public static void main(String[] args) {
        new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("匿名内部类实现");
            }
        }).start();

        new Thread(() -> {
            System.out.println("Lambda实现");
        }).start();

        Lambda lambda = new Lambda();
        lambda.printFunc(() -> System.out.println("hello, lambda"));
    }

    public void printFunc(Functional func) {
        func.accept();
    }

    interface Functional {
        void accept();
    }

}
```

输出

``` output
匿名内部类实现
Lambda实现
hello, lambda
```

**更多实例**

``` java
// 1. 不需要参数,返回值为 5  
() -> 5  
  
// 2. 接收一个参数(数字类型),返回其2倍的值  
x -> 2 * x  
  
// 3. 接受2个参数(数字),并返回他们的差值  
(x, y) -> x – y  
  
// 4. 接收2个int型整数,返回他们的和  
(int x, int y) -> x + y  
  
// 5. 接受一个 string 对象,并在控制台打印,不返回任何值(看起来像是返回void)  
(String s) -> System.out.print(s)
```

## 前提条件

Lambda的语法非常简洁，但并不是可以随便使用的，使用时有几个条件需要特别注意：

- **方法的参数**或者**局部变量类型**必须为**接口**才能使用Lambda
- 接口中有且仅有一个抽象方法

## 函数式接口

函数式接口在Java中指的是：**有且仅有一个抽象方法的接口**。

函数式接口，即适用于函数式变成场景的接口，而Java中的函数式编程体现就是Lambda，所以函数式接口就是可以适用于Lambda使用的接口。只有确保接口中有且仅有一个抽象方法，Java中的Lambda才能顺利进行推导。

`@FunctionalInterface`注解

与`@Override`注解的作用类似，Java 8中专门为函数式接口引入了一个新的注解：`@FunctionalInterface`。该注解可以用于一个接口的定义上。

> 默认方法和静态方法不会破坏函数式接口的定义，因此如下的代码是合法的。

``` java
@FunctionalInterface
public interface FunctionalDefaultMethods {
    void method();
    default void defaultMethod() {}        
}
```

## Lambda与匿名内部类对比

- 所需要的类型不一样：匿名内部类需要的类型可以是类、抽象类、接口。Lambda表达式需要的类型必须是接口。
- 抽象方法的数量不一样：匿名内部类所需的接口中抽象方法数量随意，Lambda表达式所需的接口有且只能有一个抽象方法。
- 实现原理不同：匿名内部类是在编译后形成class，Lambda表达式是在程序运行时动态生成class。

## 常用的内置函数式接口

Lambda表达式的前提是需要有函数式接口。而Lambda使用时不需要关心接口名、抽象方法名。只关心抽象方法的参数列表和返回值类型。因此为了让我们使用Lambda方便，JDK提供了大量的函数式接口。都在`java.util.function`包下可以查看。

常用的几个接口：

### Supplier

该接口意味着“供给”，对应的Lambda表达式需要对外提供一个符合泛型类型的对象数据。

``` java
@FunctionalInterface
public interface Supplier<T> {
    T get();
}
```

### Consumer

该接口与Supplier正好相反，它不是生产一个数据，而是消费一个数据，其数据类型由泛型参数决定。

``` java
@FunctionalInterface
public interface Consumer<T> {
    void accept(T t);
}
```

### Function

该接口用来提供一个类型的数据得到另一个类型的数据，前者称为前置条件，后者称为后置条件，有参数有返回值。

``` java
@FunctionalInterface
public interface Function<T, R> {
    R apply(T t);
}
```

### Predicate

有时候需要对某种类型数据进行判断，从而得到一个布尔值的接口，这时可以使用该接口。

``` java
@FunctionalInterface
public interface Predicate<T> {
    boolean test(T t);
}
```

# 接口默认方法

Java 8 新增了接口的默认方法。

简单说，默认方法就是接口可以有实现方法，而且不需要实现类去实现其方法。

我们只需在方法名前面加个 default 关键字即可实现默认方法。

> **为什么要有这个特性？**
>
> 首先，之前的接口是个双刃剑，好处是面向抽象而不是面向具体编程，缺陷是，当需要修改接口时候，需要修改全部实现该接口的类，目前的 java 8 之前的集合框架没有 foreach 方法，通常能想到的解决办法是在JDK里给相关的接口添加新的方法及实现。然而，对于已经发布的版本，是没法在给接口添加新方法的同时不影响已有的实现。所以引进的默认方法。他们的目的是为了解决接口的修改与现有的实现不兼容的问题。

## 语法

### 默认方法语法：

``` java
public interface Vehicle {
   default void print(){
      System.out.println("我是一辆车!");
   }
}
```

### 多个默认方法

一个接口有默认方法，考虑这样的情况，一个类实现了多个接口，且这些接口有相同的默认方法，以下实例说明了这种情况的解决方法：

``` java
public interface Vehicle {
   default void print(){
      System.out.println("我是一辆车!");
   }
}
 
public interface FourWheeler {
   default void print(){
      System.out.println("我是一辆四轮车!");
   }
}
```

## 静态默认方法

Java 8 的另一个特性是接口可以声明（并且可以提供实现）静态方法。例如：

``` java
public interface Vehicle {
   default void print(){
      System.out.println("我是一辆车!");
   }
    // 静态方法
   static void blowHorn(){
      System.out.println("按喇叭!!!");
   }
}
```

> 尽管默认方法有这么多好处，但在实际开发中应该谨慎使用：在复杂的继承体系中，默认方法可能引起歧义和编译错误。

# 方法引用

方法引用使得开发者可以直接引用现存的方法、Java类的构造方法或者实例对象。方法引用和Lambda表达式配合使用，使得java类的构造方法看起来紧凑而简洁，没有很多复杂的模板代码。

方法引用使用一对冒号 **::** 

- **构造器引用：**它的语法是`Class::new`，或者更一般的`Class< T >::new`实例如下：
- **静态方法引用：**它的语法是`Class::static_method`，实例如下：
- **特定类的任意对象的方法引用：**它的语法是`Class::method`实例如下：
- **特定对象的方法引用：**它的语法是`instance::method`实例如下：

我们来用一个案例来理解一下为什么需要方法引用：

``` java
public class MethodRef {

    public static void getMax(int[] arr) {
        int sum = 0;
        for (int n : arr) {
            sum += n;
        }
        System.out.println(sum);
    }

    public static void main(String[] args) {
      	// 直接重复拷贝代码
        printMax((arr) -> {
            int sum = 0;
            for (int n : arr) {
                sum += n;
            }
            System.out.println(sum);
        });
				// 在Lambda中调用另一个函数
        printMax((arr) -> {
            getMax(arr);
        });
				// 直接使用方法引用
        printMax(MethodRef::getMax);
    }

    public static void printMax(Consumer<int[]> consumer) {
        int[] arr = new int[]{11, 12, 13, 14, 15};
        consumer.accept(arr);
    }

}
```

在main函数中的使用的三个对比，这就是减少了代码的冗余。

# Stream流

Java 8 API添加了一个新的抽象称为流Stream，可以让你以一种声明的方式处理数据。

Stream 使用一种类似用 SQL 语句从数据库查询数据的直观方式来提供一种对 Java 集合运算和表达的高阶抽象。

Stream API可以极大提高Java程序员的生产力，让程序员写出高效率、干净、简洁的代码。

这种风格将要处理的元素集合看作一种流， 流在管道中传输， 并且可以在管道的节点上进行处理， 比如筛选， 排序，聚合等。

元素流在管道中经过中间操作（intermediate operation）的处理，最后由最终操作(terminal operation)得到前面处理的结果。

``` txt
+--------------------+       +------+   +------+   +---+   +-------+
| stream of elements +-----> |filter+-> |sorted+-> |map+-> |collect|
+--------------------+       +------+   +------+   +---+   +-------+
```

以上的流程转换为 Java 代码为：

``` java
List<Integer> transactionsIds = 
widgets.stream()
             .filter(b -> b.getColor() == RED)
             .sorted((x,y) -> x.getWeight() - y.getWeight())
             .mapToInt(Widget::getWeight)
             .sum();
```

## 什么是 Stream

Stream（流）是一个来自数据源的元素队列并支持聚合操作

- 元素是特定类型的对象，形成一个队列。 Java中的Stream并不会存储元素，而是按需计算。
- **数据源** 流的来源。 可以是集合，数组，I/O channel， 产生器generator 等。
- **聚合操作** 类似SQL语句一样的操作， 比如filter, map, reduce, find, match, sorted等。

和以前的Collection操作不同， Stream操作还有两个基础的特征：

- **Pipelining**: 中间操作都会返回流对象本身。 这样多个操作可以串联成一个管道， 如同流式风格（fluent style）。 这样做可以对操作进行优化， 比如延迟执行(laziness)和短路( short-circuiting)。
- **内部迭代**： 以前对集合遍历都是通过Iterator或者For-Each的方式, 显式的在集合外部进行迭代， 这叫做外部迭代。 Stream提供了内部迭代的方式， 通过访问者模式(Visitor)实现。

## 生成流

在 Java 8 中, 集合接口有两个方法来生成流：

- **stream()** − 为集合创建串行流。
- **parallelStream()** − 为集合创建并行流。

``` java
List<String> strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
List<String> filtered = strings.stream().filter(string -> !string.isEmpty()).collect(Collectors.toList());
```

## forEach

Stream 提供了新的方法 'forEach' 来迭代流中的每个数据。以下代码片段使用 forEach 输出了10个随机数：

``` java
Random random = new Random();
random.ints().limit(10).forEach(System.out::println);
```

## map

map 方法用于映射每个元素到对应的结果，以下代码片段使用 map 输出了元素对应的平方数：

``` java
List<Integer> numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5);
// 获取对应的平方数
List<Integer> squaresList = numbers.stream().map( i -> i*i).distinct().collect(Collectors.toList());
```

## filter

filter 方法用于通过设置的条件过滤出元素。以下代码片段使用 filter 方法过滤出空字符串：

``` java
List<String>strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
// 获取空字符串的数量
long count = strings.stream().filter(string -> string.isEmpty()).count();
```

## limit

limit 方法用于获取指定数量的流。 以下代码片段使用 limit 方法打印出 10 条数据：

``` java
Random random = new Random();
random.ints().limit(10).forEach(System.out::println);
```

## sorted

sorted 方法用于对流进行排序。以下代码片段使用 sorted 方法对输出的 10 个随机数进行排序：

``` java
Random random = new Random();
random.ints().limit(10).sorted().forEach(System.out::println);
```

## 并行（parallel）程序

parallelStream 是流并行处理程序的代替方法。以下实例我们使用 parallelStream 来输出空字符串的数量：

``` java
List<String> strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
// 获取空字符串的数量
int count = strings.parallelStream().filter(string -> string.isEmpty()).count();
```

我们可以很容易的在顺序运行和并行直接切换。

## Collectors

Collectors 类实现了很多归约操作，例如将流转换成集合和聚合元素。Collectors 可用于返回列表或字符串：

``` java
List<String>strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl");
List<String> filtered = strings.stream().filter(string -> !string.isEmpty()).collect(Collectors.toList());
 
System.out.println("筛选列表: " + filtered);
String mergedString = strings.stream().filter(string -> !string.isEmpty()).collect(Collectors.joining(", "));
System.out.println("合并字符串: " + mergedString);
```

## 统计

另外，一些产生统计结果的收集器也非常有用。它们主要用于int、double、long等基本类型上，它们可以用来产生类似如下的统计结果。

``` java
List<Integer> numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5);
 
IntSummaryStatistics stats = numbers.stream().mapToInt((x) -> x).summaryStatistics();
 
System.out.println("列表中最大的数 : " + stats.getMax());
System.out.println("列表中最小的数 : " + stats.getMin());
System.out.println("所有数之和 : " + stats.getSum());
System.out.println("平均数 : " + stats.getAverage());
```

> 更多Api请翻阅文档：https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html

# Date/Time API

Java 8通过发布新的Date-Time API (JSR 310)来进一步加强对日期与时间的处理。

在旧版的 Java 中，日期时间 API 存在诸多问题，其中有：

- **非线程安全** − java.util.Date 是非线程安全的，所有的日期类都是可变的，这是Java日期类最大的问题之一。
- **设计很差** − Java的日期/时间类的定义并不一致，在java.util和java.sql的包中都有日期类，此外用于格式化和解析的类在java.text包中定义。java.util.Date同时包含日期和时间，而java.sql.Date仅包含日期，将其纳入java.sql包并不合理。另外这两个类都有相同的名字，这本身就是一个非常糟糕的设计。
- **时区处理麻烦** − 日期类并不提供国际化，没有时区支持，因此Java引入了java.util.Calendar和java.util.TimeZone类，但他们同样存在上述所有的问题。

Java 8 在 **java.time** 包下提供了很多新的 API。以下为两个比较重要的 API：

- **Local(本地)** − 简化了日期时间的处理，没有时区的问题。
- **Zoned(时区)** − 通过制定的时区处理日期时间。

新的java.time包涵盖了所有处理日期，时间，日期/时间，时区，时刻（instants），过程（during）与时钟（clock）的操作。

> https://docs.oracle.com/javase/8/docs/technotes/guides/datetime/index.html

## 本地化日期时间 API

`LocalDate/LocalTime` 和 `LocalDateTime` 类可以在处理时区不是必须的情况。代码如下：

``` java
// 获取当前的日期时间
LocalDateTime currentTime = LocalDateTime.now();
System.out.println("当前时间: " + currentTime);
        
LocalDate date1 = currentTime.toLocalDate();
System.out.println("date1: " + date1);
        
Month month = currentTime.getMonth();
int day = currentTime.getDayOfMonth();
int seconds = currentTime.getSecond();
        
System.out.println("月: " + month +", 日: " + day +", 秒: " + seconds);
        
LocalDateTime date2 = currentTime.withDayOfMonth(10).withYear(2012);
System.out.println("date2: " + date2);
        
// 12 december 2014
LocalDate date3 = LocalDate.of(2014, Month.DECEMBER, 12);
System.out.println("date3: " + date3);
        
// 22 小时 15 分钟
LocalTime date4 = LocalTime.of(22, 15);
System.out.println("date4: " + date4);
        
// 解析字符串
LocalTime date5 = LocalTime.parse("20:15:30");
System.out.println("date5: " + date5);
```

>  output
>
> ```
> 当前时间: 2016-04-15T16:55:48.668
> date1: 2016-04-15
> 月: APRIL, 日: 15, 秒: 48
> date2: 2012-04-10T16:55:48.668
> date3: 2014-12-12
> date4: 22:15
> date5: 20:15:30
> ```

## 使用时区的日期时间API

如果我们需要考虑到时区，就可以使用时区`ZonedDateTime`的日期时间API：

``` java
// 获取当前时间日期
ZonedDateTime date1 = ZonedDateTime.parse("2015-12-03T10:15:30+05:30[Asia/Shanghai]");
System.out.println("date1: " + date1);
        
ZoneId id = ZoneId.of("Europe/Paris");
System.out.println("ZoneId: " + id);
        
ZoneId currentZone = ZoneId.systemDefault();
System.out.println("当期时区: " + currentZone);
```

> output
>
> ```
> date1: 2015-12-03T10:15:30+08:00[Asia/Shanghai]
> ZoneId: Europe/Paris
> 当期时区: Asia/Shanghai
> ```

> 更多示例https://www.javacodegeeks.com/2014/04/java-8-date-time-api-tutorial-localdatetime.html

# Optional 类

Optional 类是一个可以为null的容器对象。如果值存在则isPresent()方法会返回true，调用get()方法会返回该对象。

Optional 是个容器：它可以保存类型T的值，或者仅仅保存null。Optional提供很多有用的方法，这样我们就不用显式进行空值检测。

Optional 类的引入很好的解决空指针异常。

## 类声明

以下是一个 **java.util.Optional** 类的声明：

``` java
public final class Optional<T> extends Object
```

## 类方法

| 序号 | 方法 & 描述                                                  |
| :--- | :----------------------------------------------------------- |
| 1    | **static  Optional empty()**返回空的 Optional 实例。         |
| 2    | **boolean equals(Object obj)**判断其他对象是否等于 Optional。 |
| 3    | **Optional filter(Predicate predicate)**如果值存在，并且这个值匹配给定的 predicate，返回一个Optional用以描述这个值，否则返回一个空的Optional。 |
| 4    | ** Optional flatMap(Function> mapper)**如果值存在，返回基于Optional包含的映射方法的值，否则返回一个空的Optional |
| 5    | **T get()**如果在这个Optional中包含这个值，返回值，否则抛出异常：NoSuchElementException |
| 6    | **int hashCode()**返回存在值的哈希码，如果值不存在 返回 0。  |
| 7    | **void ifPresent(Consumer consumer)**如果值存在则使用该值调用 consumer , 否则不做任何事情。 |
| 8    | **boolean isPresent()**如果值存在则方法会返回true，否则返回 false。 |
| 9    | **Optional map(Function mapper)**如果有值，则对其执行调用映射函数得到返回值。如果返回值不为 null，则创建包含映射返回值的Optional作为map方法返回值，否则返回空Optional。 |
| 10   | **static  Optional of(T value)**返回一个指定非null值的Optional。 |
| 11   | **static  Optional ofNullable(T value)**如果为非空，返回 Optional 描述的指定值，否则返回空的 Optional。 |
| 12   | **T orElse(T other)**如果存在该值，返回值， 否则返回 other。 |
| 13   | **T orElseGet(Supplier other)**如果存在该值，返回值， 否则触发 other，并返回 other 调用的结果。 |
| 14   | ** T orElseThrow(Supplier exceptionSupplier)**如果存在该值，返回包含的值，否则抛出由 Supplier 继承的异常 |
| 15   | **String toString()**返回一个Optional的非空字符串，用来调试  |

**注意：** 这些方法是从 **java.lang.Object** 类继承来的。

## Optional实例

通过以下实例来更好的了解 Optional 类的使用：

``` java
public class OptionalDemo {
   public static void main(String args[]){
   
      OptionalDemo optionalDemo = new OptionalDemo();
      Integer value1 = null;
      Integer value2 = new Integer(10);
        
      // Optional.ofNullable - 允许传递为 null 参数
      Optional<Integer> a = Optional.ofNullable(value1);
        
      // Optional.of - 如果传递的参数是 null，抛出异常 NullPointerException
      Optional<Integer> b = Optional.of(value2);
      System.out.println(optionalDemo.sum(a,b));
   }
    
   public Integer sum(Optional<Integer> a, Optional<Integer> b){
    
      // Optional.isPresent - 判断值是否存在
      System.out.println("第一个参数值存在: " + a.isPresent());
      System.out.println("第二个参数值存在: " + b.isPresent());
        
      // Optional.orElse - 如果值存在，返回它，否则返回默认值
      Integer value1 = a.orElse(new Integer(0));
        
      //Optional.get - 获取值，值需要存在
      Integer value2 = b.get();
      return value1 + value2;
   }
}
```

> output
>
> ```
> 第一个参数值存在: false
> 第二个参数值存在: true
> 10
> ```

# 参考资料

https://www.jianshu.com/p/5b800057f2d8