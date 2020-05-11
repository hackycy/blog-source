---
title: CSS入门
date: 2019-03-19 17:46:03
keywords:
    - CSS
    - CSS HACK
tags:
    - CSS
categories:
    - CSS
---

# CSS入门学习

Tool: Visual Studio Code

<!-- more -->

>文中元素=标签

# 外链样式与内链样式

## 外链
``` html
<link rel="stylesheet" type="text/css" href="style/main.css">
```

## 内链
``` html
<style type="text/css">
    ...
</style>
```

# 块元素与内联元素

## 块元素

概念：独占一行的元素，标签代表有`div`,`p`，`h1~h6`等等

>div没有任何语义的标签，而在H5中新增了一些语义标签，具体请看另一片文章关于HTML5的新特性
>块元素主要作为页面的布局，一般情况块元素用来包含内联元素。p标签不可以包含其他任何块元素

实例证明:
``` html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>Page Title</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" type="text/css" media="screen" href="main.css">
    <script src="main.js"></script>
</head>
<body>
    <p>
        哈哈哈
        <div>哈哈</div>
    </p>
</body>
</html>
```
浏览器上查看内容可以正常显示，但是检查浏览器元素的显示则解析成了
![](perror.png)

## 内联元素

概念：只占自身内容大小的元素，标签代表有`span`,`a`等

>a标签可以包含任何内联和块元素，但是除了它本身

实例证明:
``` html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>Page Title</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" type="text/css" media="screen" href="main.css">
    <script src="main.js"></script>
</head>
<body>
    <a href="#">
        <a href="#"></a>
    </a>
</body>
</html>
```
检查浏览器元素的显示则解析成了
![](aerror.png)

# 标签间的关系

标签之间的关系：
- 父标签：直接包含子标签的标签
- 子标签：直接被父标签包含的标签
- 祖先标签：直接或间接包含后代标签的标签，父标签也是祖先标签
- 后代标签：直接或间接被祖先标签包含的标签，子标签也是后代标签
- 兄弟标签：拥有相同父标签的标签

# 选择器

## 标签选择器

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style type="text/css">
        div {
            width: 200px;
            height: 200px;
            color: red;
        }
    </style>
</head>
<body>
    <div>我是一个div</div>
</body>
</html>
```

## ID选择器

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style type="text/css">
        #d1 {
            width: 200px;
            height: 200px;
            color: red;
        }
    </style>
</head>
<body>
    <div id="d1">我是一个div</div>
</body>
</html>
```

## 类选择器

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style type="text/css">
        .c1 {
            width: 200px;
            height: 200px;
            color: red;
        }
    </style>
</head>
<body>
    <div class="c1">我是一个div</div>
</body>
</html>
```

## 选择器分组（并集选择器）

语法：选择器1，选择器2，选择器3{}

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style type="text/css">
        .c1,p,span{
            color: red;
        }
    </style>
</head>
<body>
    <div class="c1">我是一个div</div>
    <p>我是一个段落</p>
    <span>我是一个span</span>
</body>
</html>
```

## 通配选择器

语法：*{}

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style type="text/css">
        * {
            padding: 0;
            margin: 0;
        }
    </style>
</head>
<body>
    <div class="c1">我是一个div</div>
    <p>我是一个段落</p>
    <span>我是一个span</span>
</body>
</html>
```

这个一般用于适配每个浏览器的默认值。


~~## 复合选择器（交集选择器)~~

~~语法：选择器1选择器2{}~~

## 后代选择器

选中指定标签的指定后代标签

语法：祖先标签 后代标签 {}

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style type="text/css">
        #d1 p{
            color: red;
        }
    </style>
</head>
<body>
    <div id="d1">
        <p>我是一个在div里的段落</p>
    </div>
    <p>我是一个段落</p>
</body>
</html>
```

## 子代选择器

选中父标签的指定子标签

语法：父标签 > 子标签 {}

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style type="text/css">
        div > p > span{
            color: red;
        }
    </style>
</head>
<body>
    <div id="d1">
        <p>我是一个在div里的段落<span>我是span</span></p>
        <span>我也是span</span>
    </div>
    <p>我是一个段落</p>
</body>
</html>
```

## 伪类选择器

伪类：专门用来表示标签的一种特殊状态

>hover和active可以被其他标签所使用，不仅只是用于a标签
>vistited伪类由于涉及到用户隐私问题，所以在该标签内仅仅只能设置color属性，其他设置也无效。
>也可能还有存在版本问题，IE6可以设置，其余基本测试失败。

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style type="text/css">
        a:link {
            color: red;
        }
        a:visited {
            color: blue;
        }
        a:hover {
            color: yellow;
        }
        a:active {
            color: orange;
        }
    </style>
</head>
<body>
    <a href="http://www.baidu.com">我是超链接</a>
</body>
</html>
```

## 伪元素（伪标签）选择器

表示元素中的一些特许位置

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style type="text/css">
       p::first-letter {
           color: red;
       }
       p::first-line {
           color: yellow;
       }
    </style>
</head>
<body>
    <p>
        我是一个很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长的文字
    </p>
</body>
</html>
```

## 属性选择器

根据元素中的属性或者属性值来选指定元素。

语法：
- 标签[属性名]{} 有这个属性的
- 标签[属性名="属性值"]{} 以属性为属性值的
- 标签^[属性名="属性值"]{} 以属性的属性值为开头的
- 标签$[属性名="属性值"]{} 以属性的属性值为结尾的
- 标签*[属性名*="属性值"]{} 以属性的属性值为包含的

<p title="hello">我是一个段落</p>，title就是其中一个属性。

title就是一个属性

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style type="text/css">
       span[class] {
           color: red;
       }
       p[title="content"] {
           color: orange;
       }
    </style>
</head>
<body>
    <span class='hh'>我是一个段落</span>
    <p title="content">
        我是一个很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长很长的文字
    </p>
</body>
</html>
```

## 结构伪类选择器

:first-child {}     选中父元素中第一个子元素
:last-child {}      选中父元素中最后一个子元素
:nth-child(n) {}    选中父元素中正数第n个子元素
:nth-last-child(n) {}    选中父元素中倒数第n个子元素
:first-of-type {}   选中第一个元素
:last-of-type {}    选中最后一个元素
:nth-child(n) {}    选中第n个元素

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style type="text/css">
       p:first-child {
           color: red;
       }
    </style>
</head>
<body>
    <p>哈哈</p>
    <p>哈哈</p>
    <p>哈哈</p>
    <p>哈哈</p>
</body>
</html>
```

## 紧邻同胞选择器


选中一个元素后的指定兄弟元素。
语法：兄弟元素前 + 兄弟元素后 {}

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style type="text/css">
       p + span {
           color: red;
       }
    </style>
</head>
<body>
    <p>哈哈</p>
    <p>哈哈</p>
    <span>我是兄弟</span>
    <p>哈哈</p>
    <p>哈哈</p>
    <span>我是最后一个span</span>
</body>
</html>
```

## 一般同胞选择器

选中一个元素后的所有指定兄弟元素

语法：兄弟元素前 ~ 兄弟元素后 {}

``` css
#r1:checked ~ .s1{
  margin-left: 0;
}
#r2:checked ~ .s1{
  margin-left: -20%;
}
#r3:checked ~ .s1{
  margin-left: -40%;
}
#r4:checked ~ .s1{
  margin-left: -60%;
}
#r5:checked ~ .s1{
  margin-left: -80%;
```

## 否定伪类选择器

可以从已经选中的元素中剔除某些元素

语法：选择器:not(选择器){}

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style type="text/css">
       p:not(.c) {
           color: red;
       }
    </style>
</head>
<body>
    <p>我是p</p>
    <p>我是p</p>
    <p class="c">我是p</p>
    <p>我是p</p>
    <p>我是p</p>
    <p>我是p</p>
</body>
</html>
```

## 样式的继承

祖先的元素会被他的后代所继承。可以利用继承的特性将一些基本的样式继承给祖先元素。
但并不是所有样式都会被后代元素所继承。
例如一些背景相关的样式，都不会被继承。

## 选择器的优先级

使用不同选择器时，选中了同一个元素并设置了相同的样式，则优先级高的优先显示。

优先级规则(数值为参考值)：

- 内联样式 优先级 1000
- ID选择器 优先级 100
- 类和伪类 优先级 10
- 元素选择器 优先级 1
- 通配选择器 优先级 0
- 继承样式 无优先级

>当选择器有多种选择器时，先将优先级相加再进行比对
>选择器的优先级不会超过它的最大数量级(虽然很少这样写)
>如果选择器的优先级相同，谁在代码的位置靠后就用谁。
>并集选择器的优先级单独计算，并集每个选择器都是独立的。

特殊情况：在样式后添加一个`!important`，则该样式会获得最高优先级，甚至超过内联样式。(尽量避免使用，多人开发时比较麻烦)

## 伪类顺序

关于a的伪类有四个,`:link`,`:visited`,`hover`,`:active`，这四个伪类优先级是一样的。

但是特殊情况`:hover`与`:active`这两种情况是相同的，都是会同时触发。由于优先级相等，所以看谁在代码后就使用谁。
所以写伪类的顺序一般都是有规则的，最好习惯写为`:link`->`:visited`->`:hover`->`:active`


# 盒模型

*CSS 框模型 (Box Model) 规定了元素框处理元素内容、内边距、边框 和 外边距 的方式。*

![](boxmodel.gif)

盒模型[属性](http://www.w3school.com.cn/cssref/index.asp#box)

一个盒子分成这么几个部分
- 内容部分
- 内边距
- 边框
- 外边距

>他们都有各自的简写方式。
>盒子大小由内容区、内边距、边框共同决定。

## 内容区

css中定义width与height定义的只是内容区的大小

## 边框

*元素的边框 (border) 是围绕元素内容和内边距的一条或多条线。CSS border 属性允许你规定元素边框的样式、宽度和颜色。*

为元素设定边框必须设置三个属性:`width`,`color`,`style`。但是大部分浏览器都会有边框的默认值。

边框样式：`border-style`
边框的宽度：`border-width`
边框的颜色：`border-color`

``` html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <style type="text/css">
        .box {
            width: 200px;
            height: 200px;

            border-width: 2px;
            border-color: yellow;
            border-style: solid;

            /*简写*/
            border: 2px green solid;
        }
    </style>
</head>
<body>
    <div class="box"></div>
</body>
</html>
```

## 内边距

*元素的内边距在边框和内容区之间。控制该区域最简单的属性是 padding 属性。CSS padding 属性定义元素边框与元素内容之间的空白区域。*

padding属性接受长度值或者百分比值，但不允许使用负值。

h1 元素的各边都有 10 像素的内边距
`h1 {padding: 10px;}`

也可以根据上、右、下、左顺序分别赋值
`h1 {padding: 10px 0.25em 2ex 20%;}`

单边属性
- `padding-top`
- `padding-right`
- `padding-bottom`
- `padding-left`

>当宽度为auto（默认值）此时指定的内边距不会影响盒子的可见宽度，而是自动修复宽度而适应内边距。

## 外边距

*围绕在元素边框的空白区域是外边距。设置外边距会在元素外创建额外的“空白”。设置外边距的最简单的方法就是使用 margin 属性，这个属性接受任何长度单位、百分数值甚至负值。*

margin 属性接受任何长度单位，可以是像素、英寸、毫米或 em。margin 可以设置为 auto。但是允许设置为负值。

>auto值一般设置水平方向，可以用来设置居中。

用法基本和padding类似。

>由于页面元素都是靠左上来进行布局，则设置左，上外边距时会影响自身盒子的位置，相反，设置右，下外边距时会影响其他盒子的位置。

## [外边距合并](http://www.w3school.com.cn/css/css_margin_collapsing.asp)

*外边距合并指的是，当两个垂直外边距相遇时，它们将形成一个外边距。合并后的外边距的高度等于两个发生合并的外边距的高度中的较大者。*

**两种情况：**

- 第一种：两边为兄弟相邻的元素时
``` html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <style type="text/css">
        .box1 {
            height: 200px;
            width: 200px;
            background-color: yellow;

            margin-bottom: 100px;
        }
        .box2 {
            height: 200px;
            width: 200px;
            background-color: green;

            margin-top: 200px;
        }
    </style>
</head>
<body>
    <div class="box1"></div>
    <div class="box2"></div>
    <!-- 两盒盒子的margin仅仅只有200px，而不是300px，仅取决于最大的那个margin而不是相加的值 -->
</body>
</html>
```
- 第二种：两边为父子关系的元素并某个边与边重叠时
``` html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <style type="text/css">
        .box2 {
            width: 200px;
            height: 100px;
            background-color: yellow;

            margin-top: 100px;
        }
        .box {
            width: 200px;
            height: 300px;
            background-color: green;
        }
    </style>
</head>
<body>
    <div class="box">
        <div class="box2"></div>
        <!-- 结果会发现box2不是相对于box移动了100px，而是box往下移动了100px，原因是因为box和box2处于父子关系，它们的上边框进行了重叠导致的-->
    </div>
</body>
</html>
```

**解决方式**

1、使用padding，但是会影响盒子原来内容大小
2、添加一个空的table标签
3、使用`:before`伪类，添加一个`display=table`属性和属性值，原理与第二点相似，但是不会添加无意义的文档标签。

## 内联元素的盒模型

内联元素无法设置width和height，其余基本和块元素的盒模型相似。

三步走：内容区、padding、margin、border

# display中的block、inline、inline-block、none

`display`属性规定元素应该生成的框(盒模型)的类型。

**说明**

这个属性用于定义建立布局时元素生成的显示框类型。对于 HTML 等文档类型，如果使用 display 不谨慎会很危险，因为可能违反 HTML 中已经定义的显示层次结构。对于 XML，由于 XML 没有内置的这种层次结构，所有 display 是绝对必要的。

主要使用的值：

| 值 | 描述 |
| ---- | ---- |
| none | 此元素不会被显示 |
| block | 此元素将显示为块级元素，此元素前后会带有换行符 |
| inline | 默认。此元素会被显示为内联元素，元素前后没有换行符 |
| inline-block | 行内块元素（CSS2.1新增） |

更多的值请参考[链接](http://www.w3school.com.cn/cssref/pr_class_display.asp)

将一个div设置为内联元素：
``` html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <style type="text/css">
        .box {
            width: 100px;
            height: 100px;

            background-color: yellow;

            display: inline;
        }
    </style>
</head>
<body>
    <div class="box">a</div>
    <!-- 会发现div的设置的宽度和高度设置无效，div已经变为内联元素 -->
</body>
</html>
```

# [box-sizing](https://developer.mozilla.org/zh-CN/docs/Web/CSS/box-sizing)

CSS 中的 box-sizing 属性定义了 user agent 应该如何计算一个元素的总宽度和总高度。可以更改用于计算元素宽度和高度的默认的 CSS 盒子模型。可以使用此属性来模拟不正确支持CSS盒子模型规范的浏览器的行为。

在 CSS 盒子模型的默认定义里，你对一个元素所设置的 width 与 height 只会应用到这个元素的内容区。如果这个元素有任何的 border 或 padding ，绘制到屏幕上时的盒子宽度和高度会加上设置的边框和内边距值。这意味着当你调整一个元素的宽度和高度时需要时刻注意到这个元素的边框和内边距。当我们实现响应式布局时，这个特点尤其烦人。

**box-sizing 属性可以被用来调整这些表现:**

- `content-box`是默认值。如果你设置一个元素的宽为100px，那么这个元素的内容区会有100px 宽，并且任何边框和内边距的宽度都会被增加到最后绘制出来的元素宽度中。
- `border-box`告诉浏览器去理解你设置的边框和内边距的值是包含在width内的。也就是说，如果你将一个元素的width设为100px,那么这100px会包含其它的border和padding，内容区的实际宽度会是width减去border + padding的计算值。大多数情况下这使得我们更容易的去设定一个元素的宽高。

**属性值**

## content-box

默认值，标准盒子模型。 width 与 height 只包括内容的宽和高， 不包括边框（border），内边距（padding），外边距（margin）。注意: 内边距, 边框 & 外边距 都在这个盒子的外部。 比如. 如果 .box {width: 350px}; 而且 {border: 10px solid black;} 那么在浏览器中的渲染的实际宽度将是370px;

**尺寸计算公式：**
width = 内容的宽度
height = 内容的高度
宽度和高度的计算值都不包含内容的边框（border）和内边距（padding）。

## border-box

`width` 和 `height` 属性包括内容，内边距和边框，但不包括外边距。这是当文档处于 Quirks模式 时Internet Explorer使用的`盒模型`。注意，填充和边框将在盒子内 , 例如, `.box {width: 350px; border: 10px solid black;}` 导致在浏览器中呈现的宽度为350px的盒子。内容框不能为负，并且被分配到0，使得不可能使用border-box使元素消失。

**尺寸计算公式：**
width = border + padding + 内容的宽度
height = border + padding + 内容的高度

## ~~padding-box~~[bad]

`width` 和 `height` 属性包括内容和内边距，但是不包括边框和外边距。只有Firefox实现了这个值，它在Firefox 50中被删除。

# visibility中的hidden、visible

visibility 属性规定元素是否可见，可以被继承。

| 值 | 描述 |
| ---- | ---- |
| visible | 默认值，元素是可见的 |
| hidden | 元素是不可见的 |
| collapse | 当在表格元素中使用时，此值可删除一行或一列，但是它不会影响表格的布局。被行或列占据的空间会留给其他内容使用。如果此值被用在其他的元素上，会呈现为 "hidden"。 |
| inherit | 规定应该从父元素继承 visibility 属性的值。 |

**使 h2 元素不可见：**

``` html
h2
  {
  visibility:hidden;
  }
```

# overflow

解决溢出内容显示,overflow 属性规定当内容溢出元素框时发生的事情。

| 值 | 描述 |
| ---- | ---- |
| visible | 默认值。内容不会被修剪，会呈现在元素框之外。 |
| hidden | 内容会被修剪，并且其余内容是不可见的。 |
| scroll | 内容会被修剪，但是浏览器会显示滚动条以便查看其余的内容。 |
| auto | 如果内容被修剪，则浏览器会显示滚动条以便查看其余的内容。 |
| inherit | 规定应该从父元素继承 overflow 属性的值。 |

**在一个狭小的div以滚动条的方式显示一段文本**
``` html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <style type="text/css">
        .box {
            width: 200px;
            height: 200px;

            overflow: auto;
        }
    </style>
</head>
<body>
    <div class="box">
        <p>上面这个操作有一些缺陷，不能序列化函数、undefined、循环引用等，详见传送门，但是也能应付一些日常情况了。
事实上，在 Props 是引用类型时，单独修改对象、数组的某个属性或下标，Vue 并不会抛出错误。当然，前提是你要非常清楚自己在做什么，并写好注释，防止你的小伙伴们疑惑。
有的同学可能知道，在组件上绑定的属性，如果没有在组件内部用 Props 声明，会默认绑定到组件的根元素上去。还是之前的栗子：。</p>
    </div>
</body>
</html>
```

# 文档流

文档流出在网页最底层，它表示的是一个网页中的位置，我们所创建的元素默认都处于文档流中。
原文是`normal flow`，至于为何翻译成文档流见仁见智。指语言文本从左到右，从上到下显示。但是`浮动`、`绝对定位`、`固定定位`这些都会导致脱离文档流。

# 浮动

float 属性定义元素在哪个方向浮动。以往这个属性总应用于图像，使文本围绕在图像周围，不过在 CSS 中，任何元素都可以浮动。浮动元素会生成一个块级框，而不论它本身是何种元素。

如果浮动非替换元素，则要指定一个明确的宽度；否则，它们会尽可能地窄。

| 值 | 描述 |
| ---- | ---- |
| left | 元素向左浮动 |
| right | 元素向右浮动。 |
| none | 默认值。元素不浮动，并会显示在其在文本中出现的位置。 |
| inherit | 规定应该从父元素继承 float 属性的值。 |

设置浮动会脱离文档流，元素会尽量向父元素或者遇到其他元素的左上或者右上靠。如果浮动元素上边是一个块元素，则浮动元素不会浮动超过这个块元素。

>浮动的元素不会盖住文字，会自动环绕在浮动元素周围。
>当块元素设置float脱离文档流后，宽和高则默认是变成被内容撑开。
>内联元素使用float脱离文档流后，会变成块元素。

# 清除浮动

**`clear` 属性规定元素的哪一侧不允许其他浮动元素。**

clear 属性定义了元素的哪边上不允许出现浮动元素。在 CSS1 和 CSS2 中，这是通过自动为清除元素（即设置了 clear 属性的元素）增加上外边距实现的。在 CSS2.1 中，会在元素上外边距之上增加清除空间，而外边距本身并不改变。不论哪一种改变，最终结果都一样，如果声明为左边或右边清除，会使元素的上外边框边界刚好在该边上浮动元素的下外边距边界之下。

| 值 | 描述 |
| ---- | ---- |
| left | 在左侧不允许浮动元素。 |
| right | 在右侧不允许浮动元素。 |
| both | 在左右两侧均不允许浮动元素。 |
| none | 默认值。允许浮动元素出现在两侧。 |
| inherit | 规定应该从父元素继承 clear 属性的值。 |

>both是清除影响对当前元素影响最大的元素
>对兄弟元素有效，父子是无效的。
>清除浮动`clean`属性，清除其他浮动元素对当前元素的影响

实例：

``` html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <style type="text/css">
        .box1 {
            width: 100px;
            height: 100px;

            background-color: yellow;

            float: right;
        }
        .box2 {
            width: 200px;
            height: 200px;

            background-color: blue;

            clear: right;
        }
    </style>
</head>
<body>
    <div class="box1"></div>
    <div class="box2"></div>
    <!-- 如果box2不设置clear，则box2的位置将被顶到box1的高度上，2⃣而设置了clear后，box2还在原来的位置 -->
</body>
</html>
```

## 高度塌陷问题

原因：在文档流中，父元素高度默认是由子元素的高度撑起来的，但是设置浮动后，子元素脱离了文档流，则导致了父元素的高度塌陷。

``` html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <style type="text/css">
        
        .parent {
            border: 2px red solid;
        }

        .sun {
            height: 200px;
            width: 100px;

            background-color: yellow;

            float: right;
        }

    </style>
</head>
<body>
    <div class="parent">
        <div class="sun">
            
        </div>
    </div>
</body>
</html>
```

### 解决方式一

BFC:Block Formatting （IE6以下并不支持）Context，W3C标准中，页面中都有一个隐含的属性。默认是关闭的，当打开的时候，会出现以下特性：
- 父元素的垂直外边距不会与子元素重叠
- 开启BFC的元素不会被浮动元素所覆盖
- 开启BFC的元素可以包含浮动的子元素

如何开启BFC（无法直接开启）
- 设置元素浮动
- 开启元素绝对定位
- 设置元素的display的值为inline-block
- 设置元素的overflow的值为非visible的值（推荐hidden）【该方式最简单的方式，副作用最小】

>兼容IE6 则可以使用相似于BFC的，成为`hasLayout`，开启方式则设置`zoom:1`即可。但是该方式仅支持IE8以下。如需完全兼容和其他浏览器，则设置两者同时设置。或者指定元素宽度。

### 解决方式二

利用清除浮动，直接在高度塌陷的父元素的最后，添加一个空白的div，由于该元素没有浮动，所以它是可以撑开父元素的高度的，对其进行清除浮动，消除了上一个元素的浮动影响，即可撑起父元素的高度，且该方案兼容性比方案一更高，影响度最小。但是添加了一个毫无意义的div标签，可读性变差了。

``` html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <style type="text/css">
        
        .parent {
            border: 2px red solid;
        }

        .sun {
            height: 200px;
            width: 100px;

            background-color: yellow;

            float: right;
        }

        .clearfix {
            clear: both;
        }

    </style>
</head>
<body>
    <div class="parent">
        <div class="sun"></div>
        <div class="clearfix"></div>
    </div>
</body>
</html>
```

### 解决方式三

**利用`::after`伪元素选择器。**

>`::after` 选择器在被选元素的内容后面插入内容。
>请使用 content 属性来指定要插入的内容。

通过alter伪元素向元素添加一个空白的块元素，然后对其清除元素。和方式二的原理相同，但是该方式可读性更高。不会在html中添加毫无意义的空白标签。

``` html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <style type="text/css">
        
        .parent {
            border: 2px red solid;
        }

        .sun {
            height: 200px;
            width: 100px;

            background-color: yellow;

            float: right;
        }

        /*.clearfix {
            clear: both;
        }*/

        .clearfix::after {
            content: "";
            clear: both;
            display: block;
        }

    </style>
</head>
<body>
    <div class="parent clearfix">
        <div class="sun"></div>
    </div>
</body>
</html>
```

>display的值可以为block，也可以为table。

**标准模版**

``` css 
.clearfix {
    *zoom: 1;
}
 .clearfix::after {
    content: "";
    display: block;
    clear: both;
    visibility: hidden;
    height: 0;
    line-height: 0;
}       
```

# 定位

定位指的是将指定的元素摆放在页面中指定的位置，通过定位可以任意的摆放元素。

这个属性定义建立元素布局所用的定位机制。任何元素都可以定位，不过绝对或固定元素会生成一个块级框，而不论该元素本身是什么类型。相对定位元素会相对于它在正常流中的默认位置偏移。默认值`static`。
元素的位置通过 "left", "top", "right" 以及 "bottom" 属性进行规定。

| 值 | 描述 |
| ---- | ---- |
| absolute | 生成绝对定位的元素，相对于 static 定位以外的第一个父元素进行定位。 |
| fixed | 生成绝对定位的元素，相对于浏览器窗口进行定位。 |
| relative | 生成相对定位的元素，相对于其正常位置进行定位。 |
| static | 默认值。没有定位，元素出现在正常的流中（忽略 top, bottom, left, right 或者 z-index 声明）。 |
| inherit | 规定应该从父元素继承 position 属性的值。 |

## 相对定位

相对定位设置了，但不设置偏移量则也没有效果
相对定位是相对于元素在文档流原来的位置进行定位
相对定位不会脱离文档流
相对定位会使得元素提升一个层级
相对定位不会改变元素的性质，块元素依然是块元素。内联依然也依然是内联。

``` html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <style type="text/css">
        .box1 {
            width: 200px;
            height: 200px;
            background-color: yellow;
        }
        .box2 {
            width: 200px;
            height: 200px;
            background-color: green;

            position: relative;
            left: 100px;
            top: 100px;
        }
        .box3 {
            width: 200px;
            height: 200px;
            background-color: blue;
        }
    </style>
</head>
<body>
    <div class="box1"></div>
    <div class="box2"></div>
    <!-- 相对于原来box2的位置进行偏移向右100像素，向下100像素 -->
    <div class="box3"></div>
</body>
</html>
```

## 绝对定位

开启绝对定位会使元素脱离文档流
绝对定位是相当于离它最近的开启了定位的祖先元素进行定位的
如果所有的祖先元素都没有开启定位，则相对于浏览器窗口进行定位
绝对定位会使得元素提升一个层级并改变元素的性质

**如果祖先元素没有任何一个开启了定位，则是相对于浏览器窗口进行定位**

``` html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <style type="text/css">
        .box1 {
            width: 200px;
            height: 200px;
            background-color: yellow;
        }
        .box2 {
            width: 200px;
            height: 200px;
            background-color: green;
        }
        .box3 {
            width: 200px;
            height: 200px;
            background-color: blue;
        }
        .box4 {
            width: 100px;
            height: 100px;
            background-color: black;

            position: absolute;
            left: 0;
            top: 0;
        }
    </style>
</head>
<body>
    <div class="box1"></div>
    <div class="box2">
        <div class="box4"></div>
        <!-- 祖先元素都没有开启定位，则box4的原点位置则是浏览器左上角 -->
    </div>
    <div class="box3"></div>
</body>
</html>
```

**开启box2元素的定位，则box4原点的位置发生改变**

``` html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <style type="text/css">
        .box1 {
            width: 200px;
            height: 200px;
            background-color: yellow;
        }
        .box2 {
            width: 200px;
            height: 200px;
            background-color: green;

            position: relative;
        }
        .box3 {
            width: 200px;
            height: 200px;
            background-color: blue;
        }
        .box4 {
            width: 100px;
            height: 100px;
            background-color: black;

            position: absolute;
            left: 0;
            top: 0;
        }
    </style>
</head>
<body>
    <div class="box1"></div>
    <div class="box2">
        <div class="box4"></div>
        <!-- 最近的祖先元素box2开启了定位，则原点位置发生了改变，为box2块元素的左上角 -->
    </div>
    <div class="box3"></div>
</body>
</html>
```

 >一般情况自元素开启绝对定位，则父元素会开启相对定位

## 固定定位

固定定位也是绝对定位的一种，大部分特点和绝对定位一样。但是它永远都相对于浏览器窗口进行定位，不管父元素到底有没有开启定位。而且只会固定在浏览器窗口的位置，不会因为其他因素（滚动条等）而改变元素位置。

``` html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <style type="text/css">
        body {
            height: 5000px;
        }
        .box {
            height: 500px;
            width: 80px;

            position: fixed;
            background-color: yellow;
        }
    </style>
</head>
<body>
    <div class="box"></div>
    <!-- 固定定位该元素不会因为浏览器页面滚动而发生位置的改变 -->
    <p>看看我有没有滚动啊啊啊啊啊啊啊啊啊啊啊</p>
</body>
</html>
```

应用可以用于底部的返回，导航漂浮菜单

# 元素层级

如果定位元素处于相同层级，则后面的元素会盖住前面的元素（结构上）

也可以通过`z-index`来修改元素层级，但是没有开启定位的元素不起作用。

父元素的层级就算比子元素高，但是也不会盖住子元素。

``` html
<html>
<head>
<style type="text/css">
img.x
{
position:absolute;
left:0px;
top:0px;
z-index:-1
}
</style>
</head>

<body>
<h1>这是一个标题</h1>
<img class="x" src="/i/eg_mouse.jpg" /> 
<p>默认的 z-index 是 0。Z-index -1 拥有更低的优先级。</p>
</body>

</html>
```

# opacity

opacity 属性设置元素的不透明级别。
值为`0-1`区间。0表示完全透明，1是完全不透明。

>在IE8以及以下不支持，需要支持则使用如下属性代替，`filter`滤镜，值为`alpha(opacity=50)`,区间0-100,相当于opacity=0.5

``` html
<!DOCTYPE html>
<html>
<head>
<style> 
body {
background-color:yellow;
}
div
{
background-color:red;
opacity:0.5;
filter:Alpha(opacity=50); /* IE8 以及更早的浏览器 */
}
</style>
</head>
<body>

<div>本元素的不透明度是 0.5。请注意，文本和背景色都受到不透明级别的影响。</div>

</body>
</html>
```

# 背景

background 简写属性在一个声明中设置所有的背景属性。

可以设置如下属性：
- background-color        规定要使用的背景颜色。
- background-position     规定背景图像的位置。
- background-size         规定背景图片的尺寸。
- background-repeat       规定如何重复背景图像。
- background-origin       规定背景图片的定位区域。    
- background-clip         规定背景的绘制区域。
- background-attachment   规定背景图像是否固定或者随着页面的其余部分滚动。
- background-image        规定要使用的背景图像。

简写属性：没有顺序，也没有规定有多少个值。
`background: #00FF00 url(bgimage.gif) no-repeat fixed top;`

>IE8 以及更早的浏览器不支持一个元素多个背景图像。
>IE7 以及更早的浏览器不支持 "inherit"。IE8 需要 !DOCTYPE。IE9 支持 "inherit"。

## IE6不支持PNG24

在IE6中对图片格式为PNG24的支持度不高，解决方式
- 将png转为png-8格式即可，但是清晰度会有一点损失。
- 使用JS第三方库ie6_png

## 按钮背景闪烁

有一个按钮，按钮的状态分别设置了三个背景，上代码

``` html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <style type="text/css">
        .btn {
            display: block;
            width: 93px;
            height: 29px;
            background: url('img/link.png');
        }
        .btn:hover {
            background: url('img/hover.png');
        }
        .btn:active {
            background: url('img/active.png');
        }
    </style>
</head>
<body>
    <a class="btn"></a>
</body>
</html>
```

造成原因：浏览器外部资源不是同时加载，而是资源被使用才会去加载资源，而上述的例子中，`link.png`被加载了，但是`hover`和`active`下状态没有马上触发，所以没有加载该资源图片，当状态被触发的时候该状态的图片才会加载，但是加载图片并不是立刻能够完成的，所以在加载的过程中有一段时间，背景图片无法显示，造成了背景闪烁。为了解决将图片整合成一张图片，利用`background-position`来分别显示各自的位置，这种图片又称`精灵图/雪碧图`。

解决方法:
``` html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <style type="text/css">
        .btn {
            display: block;
            width: 93px;
            height: 29px;

            background: url('img/btn.png');

        }
        .btn:hover {
            background: url('img/btn.png');
            background-position: 93px 0;
        }
        .btn:active {
            background: url('img/btn.png');
            background-position: 186px 0;
        }
    </style>
</head>
<body>
    <a class="btn"></a>
</body>
</html>
```

## CSS精灵图/雪碧图

CSS Sprite(CSS 精灵), 也称作 雪碧图。

将导航背景图片，按钮背景图片等有规则的合并成一张背景图，即将多张图片合为一张整图，然后用background-position”来实现背景图片的定位技术。

**优势**

- 通过图片整合来减少对服务器的请求次数，从而提高 页面的加载速度
- 通过整合图片来减小图片的体积

图片整合原则：
- 边切图边整合
- 定位时避免使用bottom,right等，用具体的数值，为了避免当你的宽度或高度上扩展sprites图时出现位置的错误
- 将小图标预留足够的空间，因为使用这些图标元素通常会有大量的内容而且可能需要扩展的间距，以至于其它的图片可能会意外出现在本区域内。一般的情况下，会将这些小图标整合到文件的最右侧
- 单张整合好的sprite图片在100KB以内
- 按分类整合图片
- 为了方便计算尺寸，一般情况会将sprites图的坐标计算成整数倍

# 表格

简单用法不介绍了。table标签，tr行标签，td单元格标签，th表头标签。

## 去除table与td的距离

通过`border-spacing`属性设置这个距离。

## 表格边框合并

通过`border-collapse`设置为`collapse`即可，一旦设置`border-spacing`自动失效。

## 长表格

有一些情况下，表格是非常长的，则这时候需要将表格分为三个部分表头，表主体，表底部。
则html提供了三个标签`thead`,`tbody`,`tfoot`来分别表示进行区分。作用就是区分表格的不同部分。如果表格没有写tbody标签，则浏览器会默认添加一个tbody。

# CSS Hack

有一些特殊情况，，有一些代码只需要运行在特定的浏览器（IE6等），则可以使用CSS Hack来解决，这段代码只在某些浏览器中识别，而其他浏览器等不会识别。

以下列举的只是一小部分，像火狐等浏览器都有自己的HACK，但是相对于一般用于IE更广法。

>不到万不得已的情况尽量不要使用

## 条件Hack

用于选择IE浏览器及IE的不同版本
if条件Hack是HTML级别的（包含但不仅是CSS的Hack，可以选择任何HTML代码块）

>仅对IE10以下有效，其余浏览器解析为html注释。

**判断IE**
``` html
<!--[if IE]>
    ...
<![endif]-->
```

**判断IE6**
``` html
<!--[if IE 6]>
    ...
<![endif]-->
```

**判断IE9以下（但不包括IE9）**
``` html
<!--[if lt IE 9]>
    ...
<![endif]-->
```
>lt为小于，lte为小于等于，gt为大于，gte为大于等于，!为选择指定版本外的所有IE版本(非)

## 属性Hack

选择不同的浏览器及版本
尽可能减少对CSS Hack的使用。Hack有风险，使用需谨慎
通常如未作特别说明，本文档所有的代码和示例的默认运行环境都为标准模式。
一些CSS Hack由于浏览器存在交叉认识，所以需要通过层层覆盖的方式来实现对不同浏览器进行Hack的。如下面这个例子：

``` txt 
_：选择IE6及以下。连接线（中划线）（-）亦可使用，为了避免与某些带中划线的属性混淆，所以使用下划线（_）更为合适。

*：选择IE7及以下。诸如：（+）与（#）之类的均可使用，不过业界对（*）的认知度更高

\9：选择IE6+

\0：选择IE8+和Opera

[;property:value;];：
选择webkit核心浏览器（Chrome,Safari）。IE7及以下也能识别。中括号内外的3个分号必须保留，第一个分号前可以是任意规则或任意多个规则

[;color:#f00;]; 与 [color:#f00;color:#f00;]; 与 [margin:0;padding:0;color:#f00;]; 是等价的。生效的始终是中括号内的最后一条规则，所以通常选用第一种写法最为简洁
```

案例代码：
``` html
<!DOCTYPE html>
<html lang="zh-cn">
<head>
<meta charset="utf-8" />
<title>属性级Hack_CSS参考手册_web前端开发参考手册系列</title>
<style>
h1{margin:10px 0;font-size:16px;}
.test{
    color:#c30;          /* For Firefox */
    [;color:#ddd;];      /* For webkit(Chrome and Safari) */
    color:#090\0;        /* For Opera */
    color:#00f\9;        /* For IE8+ */
    *color:#f00;         /* For IE7 */
    _color:#ff0;         /* For IE6 */
}
</style>
</head>
<body>
<div class="test">在不同浏览器下看看我的颜色吧</div>
</body>
</html>
            
```

## 选择符Hack

选择不同的浏览器及版本
尽可能减少对CSS Hack的使用。Hack有风险，使用需谨慎
通常如未作特别说明，本文档所有的代码和示例的默认运行环境都为标准模式。
一些CSS Hack由于浏览器存在交叉认识，所以需要通过层层覆盖的方式来实现对不同浏览器进行Hack的。

>用得比较少

``` css
* html .test{color:#090;}       /* For IE6 and earlier */
* + html .test{color:#ff0;}     /* For IE7 */
.test:lang(zh-cn){color:#f00;}  /* For IE8+ and not IE */
.test:nth-child(1){color:#0ff;} /* For IE9+ and not IE */
```

# 参考链接

http://www.w3school.com.cn/css
