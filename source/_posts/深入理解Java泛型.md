---
title: 深入理解Java泛型
date: 2020-07-23 13:21:29
tags:
    - JAVA
categories:
    - JAVA
---

# 泛型的定义

**泛型**，即`参数化类型`。就是将类型由原来的具体的类型参数化，类似于方法中的变量参数，此时类型也定义成参数形式（可以称之为类型形参），然后在使用/调用时传入具体的类型（类型实参）。
例如：`GenericClass**<T>**{}`

> 一些常用的泛型类型变量：
> E：元素（Element），多用于java集合框架
> K：关键字（Key）
> N：数字（Number）
> T：类型（Type）
> V：值（Value）

<!-- more -->

先来看个简单的例子，

``` java
List<String> list = new ArrayList();
list.add("1");
list.add("2");
list.add(2);
```

运行后会发现报了一个错误

![](https://github.static.si-yee.com/posts/generic/1.png)

这里可以看出来在代码编写阶段就已经报错了，不能往string类型的集合中添加int类型的数据。

那可不可以往List集合中添加多个类型的数据呢，答案是可以的，其实我们可以把list集合当成普通的类也是没问题的，那么就有下面的代码:

``` java
List list = new ArrayList();
list.add("1");
list.add("2");
list.add(2);
for (int i = 0; i < list.size(); i++) {
	System.out.println(list.get(0));
}
```

从这里可以看出来，不定义泛型也是可以往集合中添加数据的，**所以说泛型只是一种类型的规范，在代码编写阶段起一种限制。**

下面我们通过例子来介绍泛型背后数据是什么类型。

``` java
import java.lang.reflect.Field;
import java.lang.reflect.Method;

public class Demo01 {

    public static void main(String[] args) {
        BaseBean<String> bean = new BaseBean<>();
        bean.setValue("China");
        try {
            Field value = bean.getClass().getDeclaredField("value");
            Class<?> type = value.getType();
            String name = type.getName();
            System.out.println("type: " + name);

            // 获取方法上的泛型类型
            Method getValue = bean.getClass().getDeclaredMethod("getValue");
            Object invoke = getValue.invoke(bean);
            String methodName = invoke.getClass().getName();
            System.out.println("methodName: " + methodName);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

}

class BaseBean<T> {
    T value;

    public T getValue() {
        return value;
    }

    public void setValue(T value) {
        this.value = value;
    }
}

```

输出结果：

``` bash
> Task :Demo01.main()
type: java.lang.Object
methodName: java.lang.String
```

从日志上看到通过反射获取到的属性是`Object`类型的，在方法中返回的是`String`类型，因此可以思考在`getValue`方法里面实际是做了个强转的动作，将`Object`类型的value强转成`String`类型。

因为泛型只是为了约束我们规范代码，而对于编译完之后的class交给虚拟机后，对于虚拟机它是没有泛型的说法的，所有的泛型在它看来都是`Object`类型，因此**泛型擦除**是对于虚拟机而言的。

下面我们再来看一种泛型结构：

``` java
class BaseBean2<T extends String> {
    T value;

    public T getValue() {
        return value;
    }

    public void setValue(T value) {
        this.value = value;
    }
}
```

将泛型加了个关键字`extends`，对于泛型写得多的伙伴们来说，`extends`是约束了泛型是向下继承的，最后我们通过反射获取value的类型是String类型的，因此这里也不难看出，加`extends`关键字其实最终目的是约束泛型是属于哪一类的。所以我们在编写代码的时候如果没有向下兼容类型，会警告错误的：

![](https://github.static.si-yee.com/posts/generic/2.png)

既然说了泛型其实对于jvm来说都是Object类型的，那咱们直接将类型定义成`Object`不就是的了，这种做法是可以，但是在拿到`Object`类型值之后，自己还得强转，因此泛型减少了代码的强转工作，而将这些工作交给了虚拟机。

比如下面我们没有定义泛型的例子：

``` java
class BaseBean3 {
    Object value;

    public Object getValue() {
        return value;
    }

    public void setValue(Object value) {
        this.value = value;
    }
}
```

势必在getValue的时候代码有个强转的过程，因此在能用泛型的时候，尽量用泛型来写，而且我认为一个好的架构师，业务的抽取是离不开泛型的定义。

> 常见的泛型主要有作用在普通类上面，作用在抽象类、接口、静态或非静态方法上。

# **类上面的泛型**

``` java
public class BaseBean<T> {
    public String errMsg;
    public T data;
    public int status;
}
```

# **抽象类或接口上的泛型**

``` java
public abstract class BaseAdapter<T> {
    List<T> DATAS;
}

public interface Factory<T> {
    T create();
}


public static <T> T getData() {
    return null;
}
```

# **多元泛型**

``` java
public interface Base<K, V> {
    void setKey(K k);

    V getValue();
}
```

# **泛型二级抽象类或接口**

``` java
public interface BaseCommon<K extends Common1, V> extends Base<K, V> {
}

public abstract class BaseCommon<K extends Common1, V> implements Base<K, V> {
}
```

# **抽象里面包含抽象**

``` java
public interface Base<K, V> {

   void addNode(Map<K, V> map);

   Map<K, V> getNode(int index);
}

public abstract class BaseCommon<K, V> implements Base<K, V> {
   
   LinkedList<Map<K, V>> DATAS = new LinkedList<>();

   @Override
   public void addNode(Map<K, V> map) {
       DATAS.addLast(map);
   }

   @Override
   public Map<K, V> getNode(int index) {
       return DATAS.get(index);
   }

}
```

# **<?>通配符**

`<?>通配符`和 `<T>` 区别是`<?>`在你不知道泛型类型的时候，可以用`<?>`通配符来定义，下面通过一个例子来看看`<?>`的用处：

``` java
class BaseBean<T> {
    T value;

    public T getValue() {
        return value;
    }

    public void setValue(T value) {
        this.value = value;
    }
}

class Common {}

class Common1 extends Common {
}
```

![](https://github.static.si-yee.com/posts/generic/3.png)

在定义的时候将`Common`的泛型指向`Common1`的泛型，可以看到直接提示有问题，这里可以想，虽然`Common1`是继承自`Common`的，但是并不代表`BaseBean`之间是等量的，在开篇也讲过，如果泛型传入的是什么类型，那么在`BaseBean`中的`getValue`返回的类型就是什么，因此可以想两个不同的泛型类肯定是不等价的，但是如果我这里写呢：

``` java
public static void main(String[] args) {
    BaseBean<Common> commonBaseBean = new BaseBean<>();
    
    BaseBean<?> common1BaseBean = commonBaseBean;
    try {
        
        Method setValue = common1BaseBean.getClass().getDeclaredMethod("setValue", Object.class);
        setValue.invoke(common1BaseBean, "123");
        Object value = common1BaseBean.getValue();
        System.out.println("result:" + value);
    } catch (NoSuchMethodException e) {
        e.printStackTrace();
    } catch (IllegalAccessException e) {
        e.printStackTrace();
    } catch (InvocationTargetException e) {
        e.printStackTrace();
    }
}
```

在上面如果定义的泛型是通配符是可以等价的，因为此时的setValue的参数是Object类型，所以能直接将上面定义的泛型赋给通配符的BaseBean。

**通配符不能定义在类上面、接口或方法上，只能作用在方法的参数上**

![](https://github.static.si-yee.com/posts/generic/4.png)

其他的几种情况自己去尝试，正确的使用通配符:

``` java
public void setClass(Class<?> class) {}
```

# 上限以及下限泛型

**`<T extends >`、`<T super >`、`<? extends >`、`<? super >`**

`<T extends **>`表示上限泛型、`<T super **>`表示下限泛型
为了演示这两个通配符的作用，增加了一个类:

``` java
public class Demo01 {

    public static void main(String[] args) {
        BaseBean<Common> beanCommon = new BaseBean();
        BaseBean<BaseCommon> baseCommonBaseBean = beanCommon;
    }

}

class BaseBean<T extends Common> {
    T value;

    public T getValue() {
        return value;
    }

    public void setValue(T value) {
        this.value = value;
    }
}

class BaseCommon {}

class Common extends BaseCommon {}
```

会看到出现以下错误

`Type parameter 'BaseCommon' is not within its bound; should extend 'Common'`

第二个定义的泛型是不合法的，因为`BaseCommon`是`Common`的父类，超出了`Common`的类型范围。

**`<T super >`不能作用在类、接口、方法上，只能通过方法传参来定义泛型**
在BaseBean里面定义了个方法：

``` java
public class Demo01 {

    public static void main(String[] args) {
        BaseBean<Common> beanCommon = new BaseBean();
        beanCommon.add(Common.class);
        beanCommon.add(Common1.class); //出现报错
    }

}

class BaseBean<T extends Common> {
    T value;

    public T getValue() {
        return value;
    }

    public void setValue(T value) {
        this.value = value;
    }

    public void add(Class<? super Common> clazz) {
      // 增加的方法
    }

}

class BaseCommon {}

class Common extends BaseCommon {}

class Common1 extends  Common{}

```

可以看到当传进去的是`Common1.class`的时候是不合法的，因为在`add`方法中需要传入`Common`父类的字节码对象，而`Common1`是继承自`Common`，所以直接不合法。

> 在实际开发中其实知道什么时候定义什么类型的泛型就ok，在mvp实际案例中泛型用得比较广泛，大家可以根据实际项目来找找泛型的感觉，只是面试的时候需要理解类型擦除是针对谁而言的。

# 类型擦除

其实在开篇的时候已经通过例子说明了，通过反射绕开泛型的定义，也说明了类中定义的泛型最终是以Object被jvm执行。

所有的泛型在jvm中执行的时候，都是以`Object`对象存在的，加泛型只是为了一种代码的规范，避免了开发过程中再次强转。
**泛型信息只存在于代码编译阶段，在进入 JVM 之前，与泛型相关的信息会被擦除掉，专业术语叫做类型擦除。**



**摘录自**：

www.jianshu.com/p/dd34211f2565