---
title: CSS经典面试案例详解
date: 2019-03-28 23:29:03
keywords:
    - CSS2.1
tags:
    - CSS
    - 面试
categories:
    - CSS
---

# 包含块

一个元素的尺寸和位置经常受其包含块的影响，大多数情况下，包含块就是这个元素最近的祖先元素的内容去，但是也不是总是这样的。
<!-- more -->

当一个客户端代理（比如说浏览器）展示一个文档时，对每一个元素，它都产生了一个盒子。每一个盒子都被划分为四个区域。
- 内容区
- 内边距区
- 边框区
- 外边距区
![](boxdim.png)

很多被误导的观念：一个元素的包含块就是他的父元素的内容区，但并非是这样的。

## 包含块有什么影响？

元素的尺寸及位置，常常会受它的包含块所影响。对于一些属性，例如 width, height, padding, margin，绝对定位元素的偏移值 （比如 position 被设置为 absolute 或 fixed），当我们对其赋予百分比值时，这些值的计算值，就是通过元素的包含块计算得来。

## 什么是包含块

有这么大的影响，那么包含块到底是什么？
如果对于浮动元素，其包含块定义为最近的块级祖先元素。但是对于定位的元素则行为相对复杂了。

+ “根元素”的包含块（也被称为初始包含块）由用户代理建立。在HTML中，根元素就是html元素。不过有些浏览器会使用body作为根元素。在大多数浏览器中，初始包含块是一个视窗大小的矩形。~~但不代表就是视口。~~
>在电脑图形学里面，视口代表了一个可看见的多边形区域（通常来说是矩形）。在浏览器范畴里，它代表的是浏览器中网站可见内容的部分。

+ 对于一个非根元素，如果其`position`值是`relative`或`static`，包含块则由最近的块级框、表单元格或行内块祖先框的内容边界构成。
>当时网页中基本不会使用表单元格、行内块来作为页面的基本布局。

+ 对于一个非根元素，如果其`position`值是`absolute`，包含块设置为最近的`position`值不是`static`的祖先元素（可以是任何类型）。
    - 如果这个祖先元素是块级元素，包含块则设置为该元素的内边距边界，换句话说就是由边框界定的区域。
    - 如果这个祖先元素是行内元素，包含块则设置为该祖先元素的内容边界。在从左向右的语言中，包含块的上边界和左边界是该祖先元素中第一个框内容区的上边界和左边界，包含块的下边界和右边界是最后一个框内容区的下边界和右边界。而从右向左读的语言中，包含块的右边界对应于第一个框的右内容边界，包含块的左边界则取自最后一个框的做内容边界，上下边界也是一样。
    - 如果没有祖先，元素的包含块定义为初始包含块。


**看一下案例，对于包含块的影响**

### 初始包含块

``` html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <style type="text/css">
        * {
            margin: 0;
            padding: 0;
        }
        html {
            height: 100%;
            /*margin: 30px;*/
            border: 1px red solid;
        }
        body {
            /*margin: 30px;*/
            border: 1px pink solid;
            height: 90%;
        }
    </style>
</head>
<body>

</body>
</html>
```

### 案例一

``` html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>Page Title</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <style type="text/css">
        * {
            margin: 0;
            padding: 0;
        }
        #wrap {
            background-color: beige;
            width: 400px;
            height: 800px;
        }
        #content {
            background-color: pink;
            /* 200px */
            width: 50%;  
            /* 200px */
            height: 50%;
            /* 40px */
            margin: 10%;
            /* 40px */
            padding: 10%;
        }
    </style>
</head>
<body>
    <div id="wrap">
        <div id="content"></div>
    </div>
</body>
</html>
```

content标签为静态定位，则wrap标签为他的包含块，所以content标签的高和宽的百分比值有wrap包含块的高和宽来决定，margin和padding的百分比值则由wrap包含块的宽来决定。

### 案例二

``` html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>Page Title</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <style type="text/css">
        * {
            margin: 0;
            padding: 0;
        }
        #wrap {
            background-color: yellow;
            display: inline;
        }
        #content {
            background-color: pink;
            /* 200px */
            width: 50%;  
            height: 200px;
            /* 40px */
            margin: 10%;
            /* 40px */
            padding: 10%;
        }
    </style>
</head>
<body>
    <div id="wrap">
        <div id="content"></div>
    </div>
</body>
</html>
```

与案例1相似的结构，但是wrap元素变成了内联元素，则wrap不是content的包含块，没有祖先元素能够成为content的包含块，则content的包含块为初始包含块。所以content的宽和高的百分比值将不再由wrap元素决定。

### 案例三

``` html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>Page Title</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <style type="text/css">
        * {
            margin: 0;
            padding: 0;
        }
        #wrap {
            position: absolute;
            left: 0px;
            top: 0px;
            background-color: yellow;
            width: 500px;
            height: 500px;
            padding: 50px 50px;
        }
        #content {
            position: absolute;
            /* 300px */
            width: 50%; 
            /* 150px */
            height: 25%;
            /* 60px */
            padding: 10%;
            /* 60px */
            margin: 10%;
            background-color: pink;
        }
    </style>
</head>
<body>
    <div id="wrap">
        <div id="content"></div>
    </div>
</body>
</html>
```
与案例1相似的结构，wrap和content元素都开启了绝对定位，那么wrap元素则为content元素的包含块，所以content的百分比值有wrap元素来决定，但是注意wrap的盒模型有padding值，计算时需要加上。
>如果包含块的 box-sizing 值设置为 border-box ，就没有这个问题。

### 案例四

``` html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>Page Title</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <style type="text/css">
        * {
            margin: 0;
            padding: 0;
        }
        #wrap {
            width: 400px;
            height: 300px;
            margin: 50px;
            padding: 30px;
            background-color: yellow;
        }
        #content {
            position: fixed;
            left: 0;
            top: 0;
            /* 50% vw */
            width: 50%;
            /* 50% scroll h  */
            height: 50%;
            /* 10% vw */
            margin: 10%;
            /* 10% vw */
            padding: 10%;
            background-color: pink;
        }
    </style>
</head>
<body>
    <div id="wrap">
        <div id="content"></div>
    </div>
</body>
</html>
```

content元素的包含块的定位为`fixed`，所以他的包含块即为初始包含块（屏幕上，也即是viewport）,content元素则会随着浏览器窗口的大小的变化而变化。

### 案例五

``` html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>Page Title</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <style type="text/css">
        * {
            margin: 0;
            padding: 0;
        }

        body {
            background: beige;
        }

        section {
            transform: rotate(0deg);
            width: 400px;
            height: 160px;
            background: lightgray;
        }

        p {
            position: absolute;
            left: 80px;
            top: 30px;
            width: 50%;
            /* == 200px */
            height: 25%;
            /* == 40px */
            margin: 5%;
            /* == 20px */
            padding: 5%;
            /* == 20px */
            background: cyan;
        }
    </style>
</head>

<body>
    <section>
        <p>This is a paragraph!</p>
    </section>
</body>

</html>
```

这个示例中，P 元素的 position 为 absolute，所以它的包含块是 <section>，也就是距离它最近的一个 transform 值不为 none 的父元素。

# 默认值

left、top、right、bottom、width、height默认值为auto。
margin、padding默认值为0。

# 浮动元素层级

看一个案例
``` html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <style type="text/css">
        div {
            width: 200px;
            height: 200px;
        }
        #up {
            background-color: yellow;
            float: left;
        }
        #down {
            background-color: pink;
        }
    </style>
</head>
<body>
    <div id="up">upupup</div>
    <div id="down">downdowndown</div>
</body>
</html>
```

原来`up`id的元素不开启浮动的情况下，两个块级元素上下排列，但是开启了`up`id的元素的浮动后，
`down`id的元素由于收到了`up`元素的影响顶了上去，但是发现文字并没有跟着上去。
解释：
**在浮动的情况下，元素层级只提升半层，元素分两层，一层与盒模型相关，一层与文字相关。**

# 负margin详解

[负margin用法权威指南](https://www.w3cplus.com/css/the-definitive-guide-to-using-negative-margins.html)

何为负margin

``` html
#content {margin-left:-100px;}
```

- 负margin是标准的CSS，在w3c中，margin属性值是允许出现负值的
- 负margin不是一种hack方法
- 不脱离文档流（在不使用浮动的情况下）
- 完全兼容
- 浮动会影响负margin的使用
- ~~dreamveaver不解析负margin（前端工程师不推荐使用，也不应该在设计视图中检查网站）~~

**作用在static元素上的负margin属性值
![](negative-margin-1.jpg)
当static元素没有设定成浮动的元素，上图中说明了负margin对static元素的作用。

当static元素的`margin-left/margin-right`被赋予负值时，元素将被拉进指定方向。

``` css
#box1 {margin-top : -10px;}
/*元素向上移动10px*/
```

当static元素的`margin-bottom/margin-right`被赋予负值时，会将后续的元素拖拉进来，覆盖原来的元素。

``` css
#box1 {margin-bottom : -10px;}
/*box1后续的元素将向上移动10px，box1本身不会移动*/
```

**作用在浮动元素上的负margin属性值**

``` html
<div id="mydiv1">First</div>
<div id="mydiv2">Second</div>
```
``` css
/* 应用在与浮动相反方向的负margin */
#mydiv1 {float:left; margin-right:-100px;}
```

如果给一个浮动元素加上相反方向的负margin，则会使行间距为0且内容重叠。这对于创建1列是100%宽度而其他列是固定宽度（比如100px）的自适应布局来说是非常有用的方法。

若两个元素都为浮动，且#mydiv1的元素设定margin-right为20px。这样#mydiv2会认为#mydiv1的宽度比原来宽度缩短了20px（因此会导致重叠）。但有意思的是，#mydiv1的内容不受影响，保持原有的宽度。

如果负margin等于实际宽度，则元素会被完全覆盖。这是因为元素的完全宽度等于margin，padding，border，width相加而成，所以如果负margin等于余下三者的和，那元素的实际宽度也就变成了0px。

# 圣杯布局

看圣杯布局前，先看看如何实现三列布局

**三列布局需求：**
- 两边固定，中间自适应
- 当中列要完整显示
- 当中列优先加载

## 定位实现

``` html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>Page Title</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <style type="text/css">
        * {
            margin: 0;
            padding: 0;
        }

        body {
            /* 两倍的left+right */
            min-width: 600px;
        }

        #content {
            position: relative;
        }
        #left,
        #right {
            width: 200px;
            height: 400px;
            background-color: yellow;
        }

        #left {
            position: absolute;
            left: 0;
            top: 0;
        }

        #right {
            position: absolute;
            right: 0;
            top: 0;
        }

        #middle {
            height: 400px;
            padding: 0 200px;
            background-color: pink;
        }
    </style>
</head>

<body>
    <div id="content">
        <div id="left">left</div>
        <div id="middle">middle</div>
        <div id="right">right</div>
    </div>
</body>

</html>
```

但是页面的整体布局很少会使用定位来实现，而且定位需要一个容器，容器的定位必须是相对定位，定位会提升元素层级，这样布局会相对复杂。

## 浮动实现

``` html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>Page Title</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <style type="text/css">
        * {
            margin: 0;
            padding: 0;
        }

        body {
            /* 两倍的left+right */
            min-width: 600px;
        }

        #left,
        #right {
            width: 200px;
            height: 400px;
            background-color: yellow;
        }

        #left {
            float: left;
        }

        #right {
            float: right;
        }

        #middle {
            height: 400px;
            background-color: pink;
        }
    </style>
</head>

<body>
    <div id="left">left</div>
    <div id="right">right</div>
    <div id="middle">middle</div>
</body>

</html>
```

可以看到，浮动来实现三列布局比使用定位来实现三列布局更加方便。但是也造成一个问题，中间列按照文档的加载顺序，无法做到中间列优先加载，所以圣杯布局就出现了。

## 实现圣杯布局

完整实现代码

``` html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>Page Title</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <style type="text/css">
        * {
            margin: 0;
            padding: 0;
        }

        #header,
        #footer {
            background-color: gray;
            text-align: center;
            line-height: 50px;
            height: 50px;
        }

        #left,
        #right {
            background-color: yellow;
            width: 200px;
            height: 200px;
        }

        #middle {
            /* 1、中间自适应，给予宽度100%，middle的包含块为container */
            width: 100%;
            background-color: pink;
            height: 200px;
        }

        #middle, #left, #right {
            /* 2、三个块设置浮动，且middle宽度为100%，则left，和right换行显示 */
            float: left;
        }

        #left {
            /* 3、解决left，设置margin-left为-100%，因为left的包含块和middle的包含块是同一个，
            则-100%的值等于middle的宽度，则left被拉成与middle同行显示且靠middle左边对齐 */
            margin-left: -100%;
            /* 6、将自己往外拉自身的宽度值 */
            position: relative;
            left: -200px;
        }

        #right {
            /* 4、同理，只需要设置负值为与自身宽度相同，则被拉上与middle同行显示，且右边与middle右边对其 */
            margin-left: -200px;
            /* 7、将自己往外拉自身的宽度值 */
            position: relative;
            right: -200px;
        }

        #middle {
            
        }

        #container {
            /* 5、完成后，但是middle宽度还是整个的100%，left与right其实只是盖着它们，并没有把内容设置到中间，但是这里不能简单的设置padding值，
            因为中间的宽度是100&设定死的，而且left的margin也会随之变化，
            所以要给它们三个的父元素container设置padding值，然后再将left和right往外拉 */
            padding: 0 200px;
        }

        /* 0、解决高度塌陷 */
        .clearfix {
            *zoom: 1;
        }

        .clearfix::after {
            content: "";
            display: block;
            clear: both;
        }
    </style>
</head>

<body>
    <div id="header">header</div>
    <div id="container" class="clearfix">
        <div id="middle">middle</div>
        <div id="left">left</div>
        <div id="right">right</div>
    </div>
    <div id="footer">footer</div>
</body>

</html>
```

圣杯布局结合了浮动、定位等技术，但最重要一点是使用了`负margin`的用法。

# 伪等高布局

通常我们会遇到一些需求，要求两列高度相同，但是两列内容所撑开的高度不一定相同，伪等高布局就出现了。

实现代码

``` html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style type="text/css">
        #content {
            margin: 0 400px;
            border: 1px black solid;
            /* 父容器设置内容溢出显示 */
            overflow: hidden;
        }

        #content .left {
            float: left;
            width: 400px;
            background-color: pink;
        }

        #content .right {
            float: left;
            width: 400px;
            background-color: gray;
        }

        #content .left,
        #content .right {
            /* 设置padding，让内容撑开一定高度 */
            padding-bottom: 10000px;
            /*内容边界在收缩回来。*/
            margin-bottom: -10000px;
        }

        /* 解决高度塌陷 */
        .clearfix {
            *zoom: 1;
        }

        .clearfix::after {
            content: "";
            display: block;
            clear: both;
        }
    </style>
</head>

<body>
    <div id="content" class="clearfix">
        <div class="left">
            <h1>left</h1>
            <h1>left</h1>
            <h1>left</h1>
            <h1>left</h1>
            <h1>left</h1>
            <h1>left</h1>
            <h1>left</h1>
            <h1>left</h1>
            <h1>left</h1>
            <h1>left</h1>
            <h1>left</h1>
            <h1>left</h1>
        </div>
        <div class="right">
            <h1>right</h1>
            <h1>right</h1>
            <h1>right</h1>
            <h1>right</h1>
            <h1>right</h1>
            <h1>right</h1>
            <h1>right</h1>
            <h1>right</h1>
            <h1>right</h1>
            <h1>right</h1>
            <h1>right</h1>
            <h1>right</h1>
            <h1>right</h1>
            <h1>right</h1>
            <h1>right</h1>
            <h1>right</h1>
            <h1>right</h1>
            <h1>right</h1>
            <h1>right</h1>
            <h1>right</h1>
            <h1>right</h1>
        </div>
    </div>
</body>

</html>
```

同样多列也是如此运用，但是原理还是利用了`负margin`的特性来实现。

# 双飞翼布局

因为圣杯布局外部整体布局还是会使用到定位，这样还是会对页面布局产生比较大的影响，那么双飞翼布局就出现了，对比圣杯，仅仅只是加了一个标签来搭建整体结构，不会使用到定位这些元素来影响到整体的布局。

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
            margin: 0;
            padding: 0;
        }

        #header,
        #footer {
            background-color: yellow;
            line-height: 50px;
            text-align: center;
        }

        #container .left, #container .right {
            background-color: pink;
            width: 200px;
            height: 200px;
        }

        #container .left {
            float: left;
            margin-left: -100%;
        }

        #container .right {
            float: left;
            margin-left: -200px;
        }

        #container .middle {
            float: left;
            width: 100%;
            height: 200px;
            background-color: aqua;
        }

        #container .middle .content {
            padding: 0 200px;
        }

        /* 解决高度塌陷 */
        .clearfix {
            *zoom: 1;
        }
        .clearfix::after {
            content: "";
            display: block;
            clear: both;
        }
    </style>
</head>

<body>
    <div id="header">header</div>
    <div id="container" class="clearfix">
        <div class="middle">
            <!-- 对比圣杯布局，就是在middle里再加一个元素用来做内容的容器，那么这个容器再设置padding就不会影响到两侧元素了 -->
            <div class="content">
                middle content
            </div>
        </div>
        <div class="left">left</div>
        <div class="right">right</div>
    </div>
    <div id="footer">footer</div>
</body>

</html>
```

# 滚动条

先看一段代码

``` html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style type="text/css">
        html {
            margin: 30px;
            border: 1px black solid;
        }

        body {
            margin: 30px;
            border: 1px black solid;
        }

        .box {
            height: 5000px;
        }
    </style>
</head>

<body>
    <div class="box"></div>
</body>

</html>
```

看看这时候的滚动条作用与谁身上，不是body，也不是html，而是作用与`文档`上。

再看一个案例

``` html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style type="text/css">
        html {
            height: 100%;
            margin: 30px;
            border: 1px black solid;
            overflow: scroll;
        }

        body {
            height: 100%;
            margin: 30px;
            border: 1px black solid;
            overflow: scroll;
        }

        .box {
            height: 5000px;
        }
    </style>
</head>

<body>
    <div class="box"></div>
</body>

</html>
```

body和html都添加了高度为100%的属性值，而它俩的包含块为初始包含块，所以它们两个高度为视口的高度。而两个都添加了overflow为scroll时，不仅文档出现了滚动条，而且body身上也出现了滚动条。

# 静止系统默认滚动条

看到上面的例子后，如何静止掉系统的默认滚动条。

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style type="text/css">
        html,body {
            height: 100%;
            overflow: hidden;
        }
        body {
            overflow: auto;
        }
    </style>
</head>
<body>
    <div style="height: 5000px;">
        
    </div>
</body>
</html>
```

# 解决IE6下fixed固定定位失效问题

实现代码

``` html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style type="text/css">
        /* 隐藏系统滚动条 */
        html,
        body {
            height: 100%;
            overflow: hidden;
        }

        body {
            overflow: scroll;
        }

        .test {
            position: absolute;
            left: 50px;
            top: 50px;
            width: 100px;
            height: 100px;
            background-color: pink;
        }
    </style>
</head>

<body>
    <div class="test"></div>
    <div style="height: 5000px;"></div>
</body>

</html>
```

可以看到test元素仅仅只是设置了绝对定位，但是作用基本等同于固定定位。
**解释：**因为这里隐藏了系统的默认滚动条，body显示的滚动条并不会移动视口，只有系统的滚动条才会移动视口。body的滚动条仅仅只是移动的body内的元素。而test元素的包含块是视口，所以就算滚动条怎么移动，test元素也不会移动，这就是另类解决fixed定位失效的问题解决办法。


# 粘连布局

可能会遇到一种需求，我们有一个内容`main`
- 当`main`的高度足够长的时候，`footer`应该紧跟在`main`元素后面
- 当`main`元素比较短的时候（比如小于屏幕的高度），我们期望这个`footer`元素能够粘连在屏幕底部

实现代码

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
            margin: 0;
            padding: 0;
        }

        body,
        html {
            height: 100%;
        }

        #wrap {
            background-color: yellow;
            text-align: center;
            min-height: 100%;
        }

        #wrap .main {
            padding-bottom: 50px;
        }

        #footer {
            background-color: pink;
            height: 50px;
            line-height: 50px;
            text-align: center;

            /* 将自己拉上去一个自己的高度 */
            margin-top: -50px;
        }
    </style>
</head>

<body>
    <div id="wrap">
        <div class="main">

            main<br />
            main<br />

            main<br />
            main<br />
            main<br />
            main<br />
            main<br />
<!-- 
            main<br />
            main<br />
            main<br />
            main<br />
            main<br />
            main<br />

            main<br />
            main<br />
            main<br />
            main<br />
            main<br />

            main<br />
            main<br />
            main<br />
            main<br />
            main<br />
            main<br />

            main<br />
            main<br />
            main<br />
            main<br />
            main<br />

            main<br />
            main<br />
            main<br />
            main<br /> -->

        </div>
    </div>
    <!-- 必须要放在wrap外面 -->
    <div id="footer">footer</div>
</body>

</html>
```

内容少的时候，footer粘在底部，当内容过多时滚动并紧跟内容。也是简单的`负margin`的运用。

# [BFC](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context)

理解BFC是什么前我们先来理解另外两个概念：`Box`和`FC`(即formatting context)

## Box

一个页面是由很多个`Box`组成的，元素的类型和`display`属性决定了这个`Box`的类型。不同类型的`Box`，会参与不同的Formatting Context。

`Block Level`的box会参与形成`BFC`，比如`display`值为`block`，`list-item`，`table`的元素。

`Inline Level`的box会参与形成`IFC`，比如`display`值为`inline`，`inline-table`，`inline-block`的元素。

## FC(Formatting Context)

它是W3C CSS2.1规范中的一个概念，定义的是页面中的一块渲染区域，并且有一套渲染规则，它 **决定了其子元素将如何定位**，以及和 **其他元素的关系和相互作用**。

常见的`Formatting Context`有：`Block Formatting Context`(BFC|块级格式化上下文)和`Inline Formatting Context`(IFC|行内格式化上下文)。

## IFC、BFC布局规则

### IFC布局规则

在行内格式化上下文中，框（boxes）一个接一个的水平排列，起点是包含块的顶部。水平方向上的`margin`，`border`和`padding`在框之间得到保留。框在垂直方向上可以以不同的方式对齐：它们的顶部或者底部对其，或根据其中文字的基线对其。包含那些框的长方形区域，会形成一行，叫做行框。

### BFC布局规则【重要】

- 内部的Box会在垂直方向，一个接一个的放置。
- Box垂直方向的距离由`margin`决定。属于同一个BFC的两个相邻Box的`margin`会发生重叠
- 每个元素的左外边缘（margin-left），与包含块的左边（`contain box left`)相接触（对于从左往右的格式化，否则相反）。即使存在浮动也是如此。除非这个元素自己形成了一个新的BFC。
- BFC的区域不会与`float box`重叠。
- BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。
- 计算BFC的高度时，浮动元素也参与计算。

## 块格式化上下文（Block Formatting Context，BFC）

是Web页面的可视化CSS渲染的一部分，是块盒子的布局过程发生的区域，也是浮动元素与其他元素交互的区域。

下列方式会创建 **块格式化上下文：**

- 根元素或包含根元素的元素
- 浮动元素（元素的 `float` 不是 `none`）
- 绝对定位元素（元素的 `position` 为 `absolute` 或 `fixed`）
- 行内块元素（元素的 `display` 为 `inline-block`）
- 表格单元格（元素的 `display`为 `table-cell`，HTML表格单元格默认为该值）
- 表格标题（元素的 `display` 为 `table-caption`，HTML表格标题默认为该值）
- 匿名表格单元格元素（元素的 `display`为 `table`、`table-row`、 `table-row-group`、`table-header-group`、`table-footer-group`（分别是`HTML table、row、tbody、thead、tfoot`的默认属性）或 `inline-table`）
- `overflow` 值不为 `visible` 的块元素
- `display` 值为 `flow-root` 的元素
- `contain` 值为 `layout`、`content`或 `strict` 的元素
- 弹性元素（`display`为 `flex` 或 `inline-flex`元素的直接子元素）
- 网格元素（`display`为 `grid` 或 `inline-grid` 元素的直接子元素）
- 多列容器（元素的 `column-count` 或 `column-width` 不为 `auto`，包括 `column-count` 为 1）
- `column-span` 为 `all` 的元素始终会创建一个新的BFC，即使该元素没有包裹在一个多列容器中（标准变更，Chrome bug）。

>块格式化上下文包含创建它的元素内部的所有内容.

块格式化上下文对浮动定位（参见 float）与清除浮动（参见 clear）都很重要。浮动定位和清除浮动时只会应用于同一个BFC内的元素。浮动不会影响其它BFC中元素的布局，而清除浮动只能清除同一BFC中在它前面的元素的浮动。外边距折叠（Margin collapsing）也只会发生在属于同一BFC的块级元素之间。

简单来说：BFC就是一个容器，来管理块级元素。

# IE下的BFC（hasLayout）

IE5、6、7下没有BFC的概念，但有类似于BFC相同的概念`hasLayout`。

hasLayout可以简单看作是IE5.5/6/7中的`BFC(Block Formatting Context)`。也就是一个元素要么自己对自身内容进行组织和尺寸计算(即可通过width/height来设置自身的宽高)，要么由其containing block来组织和尺寸计算。而IFC（即没有拥有布局）而言，则是元素无法对自身内容进行组织和尺寸计算，而是由自身内容来决定其尺寸（即仅能通过line-height设置内容行距，通过行距来支撑元素的高度；也无法通过width设置元素宽度，仅能由内容来决定而已）。

当hasLayout为true时(就是所谓的"拥有布局")，相当于元素产生新BFC，元素自己对自身内容进行组织和尺寸计算;

当hasLayout为false时(就是所谓的"不拥有布局")，相当于元素不产生新BFC，元素由其所属的containing block进行组织和尺寸计算。

和产生新BFC的特性一样，hasLayout无法通过CSS属性直接设置，而是通过某些CSS属性间接开启这一特性。不同的是某些CSS属性是以不可逆方式间接开启hasLayout为true。并且默认产生新BFC的只有html元素，而默认hasLayout为true的元素就不只有html元素了。

另外我们可以通过object.currentStyle.hasLayout属性来判断元素是否开启了hasLayout特性。

必须说明： **IE8及以上浏览器使用了全新的显示引擎，已经不再使用hasLayout属性，因为hasLayout属性只针对IE7以下。

**默认拥有布局的元素**

``` html
<html>, <body>
<table>, <tr>, <th>, <td>
<img>,<hr>
<input>, <button>, <select>, <textarea>, <fieldset>, <legend>
<iframe>, <embed>, <object>, <applet>,<marquee>
```

**如何触发hasLayout**

``` css
display: inline-block
height: (除 auto 外任何值)
width: (除 auto 外任何值)
float: (left 或 right)
position: absolute
writing-mode: tb-rl
zoom: (除 normal 外任意值)
```

IE7还有一些额外的属性可触发hasLayout

``` css
min-height: (任意值)
min-width: (任意值)
max-height: (除 none 外任意值)
max-width: (除 none 外任意值)
overflow: (除 visible 外任意值，仅用于块级元素)
overflow-x: (除 visible 外任意值，仅用于块级元素)
overflow-y: (除 visible 外任意值，仅用于块级元素)
position: fixed
```

使用hasLayout就是为了兼容一些项目需要运行在IE7以下版本。

# BFC实现自适应两栏布局

``` html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style type="text/css">
        #content .left {
            float: left;
            width: 200px;
            height: 200px;
            background-color: aquamarine;
        }

        #content .middle {
            height: 200px;
            background-color: green;
            overflow: hidden;
        }

        .clearfix::after {
            content: "";
            display: block;
            clear: both;
        }

        .clearfix {
            zoom: 1;
        }
    </style>
</head>

<body>
    <div id="content" class="clearfix">
        <div class="left"></div>
        <div class="middle"></div>
    </div>
</body>

</html>
```

可以使用三列布局的思想来实现两列布局，但是利用BFC的原理可以更简单的实现两列布局。由于left块开启浮动，会使得left显示在middle上层，但是middle还是占满100%，单纯用颜色判断像是两列布局，但是开启left透明度会发现middle占满100%，是浮现在上面，而让middle开启BFC，overflow为hidden即为开启了middle的BFC，BFC的区域并不会和浮动区域重叠，即可实现。

# 清除浮动解决高度塌陷的方式

## 直接给予高度

直接计算内容盒子高度，并赋予父容器高度，简单，但是毫无扩展性。

## 利用BFC

``` html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style type="text/css">
        #content {
            border: 1px black solid;
            /* 利用BFC布局原则，BFC块高度，浮动元素也会参与高度计算，只要满足开启BFC的条件即可 */
            overflow: hidden;
        }

        #content .box {
            float: left;
            width: 200px;
            height: 200px;
            background-color: aqua;
        }
    </style>
</head>

<body>
    <div id="content">
        <div class="box"></div>
    </div>
</body>

</html>
```

## br标签

`<br clear="all' />`

``` html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style type="text/css">
        #content {
            border: 1px black solid;
        }

        #content .box {
            float: left;
            width: 200px;
            height: 200px;
            background-color: aqua;
        }
    </style>
</head>

<body>
    <div id="content">
        <div class="box"></div>
        <!-- 利用br标签 -->
        <br clear="all" />
    </div>
</body>

</html>
```

## 空标签清除浮动

``` html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style type="text/css">
        #content {
            border: 1px black solid;
        }

        #content .box {
            float: left;
            width: 200px;
            height: 200px;
            background-color: aqua;
        }

        .clearfix {
            clear: both;
        }
    </style>
</head>

<body>
    <div id="content">
        <div class="box"></div>
        <!-- 空标签并清除浮动 -->
        <div class="clearfix"></div>
    </div>
</body>

</html>
```

## 伪元素清除浮动

``` html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style type="text/css">
        #content {
            border: 1px black solid;
        }

        #content .box {
            float: left;
            width: 200px;
            height: 200px;
            background-color: aqua;
        }

        .clearfix {
            *zoom: 1;
        }

        .clearfix::after {
            content: "";
            display: block;
            clear: both;
        }
    </style>
</head>

<body>
    <div id="content" class="clearfix">
        <div class="box"></div>
    </div>
</body>

</html>
```

# 垂直水平居中

## 方式1

需要已知自身元素高宽。

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style type="text/css">
        #wrap {
            position: relative;
            width: 600px;
            height: 600px;
            background-color: pink;
            margin: 0 auto;
        }

        #inner {
            position: absolute;
            left: 50%;
            top: 50%;
            margin-left: -50px;
            margin-top: -50px;
            width: 100px;
            height: 100px;
            background-color: aqua;
        }
    </style>
</head>
<body>
    <div id="wrap">
        <div id="inner">test</div>
    </div>
</body>
</html>
```

## 方式2

需要已知自身元素高宽。

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style type="text/css">
        #wrap {
            position: relative;
            width: 600px;
            height: 600px;
            background-color: pink;
            margin: 0 auto;
        }

        #inner {
            position: absolute;
            left: 0;
            top: 0;
            right: 0;
            bottom: 0;
            margin: auto auto;
            width: 100px;
            height: 100px;
            background-color: aqua;
        }
    </style>
</head>
<body>
    <div id="wrap">
        <div id="inner">test</div>
    </div>
</body>
</html>
```

原理：

绝对定位盒子特性：高宽有内容撑开下
水平方向上：left + right + width + padding + margin = 包含块padding内容区域
垂直方向上：top + bottom + width + padding + margin = 包含块padding内容区域

上述例子中：
水平方向上：0 + 0 + 100 + 0 + auto = 600
垂直方向上：0 + 0 + 100 + 0 + auto = 600
则auto等于250，即可居中。

## 方案3

无需知道元素高度，使用transform

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style type="text/css">
        #wrap {
            position: relative;
            width: 600px;
            height: 600px;
            background-color: pink;
            margin: 0 auto;
        }

        #inner {
            position: absolute;
            left: 50%;
            top: 50%;
            transform: translate(-50%,-50%);
            background-color: aqua;
        }
    </style>
</head>
<body>
    <div id="wrap">
        <div id="inner">
            testaaaaaaa<br />
            testaaaaaa<abr />
            testaaaaaa<br />
            testaaaaaa<br />
        </div>
    </div>
</body>
</html>
```

# 行高

![行高](line_height.png)

从上到下四条线分别是顶线、中线、基线、底线，很像才学英语字母时的四线三格

1.行高是指上下文本行的基线间的垂直距离，即图中两条红线间垂直距离。
2.行距是指一行底线到下一行顶线的垂直距离，即第一行粉线和第二行绿线间的垂直距离。
3.半行距是行距的一半，即区域3垂直距离/2，区域1，2，3，4的距离之和为行高，而区域1，2，4距离之和为字体size，所以半行距也可以这么算：（行高-字体size）/2 



**内容区：**底线和顶线包裹的区域，即下图深灰色背景区域。 
文本行中的每个元素都会生成一个内容区，这个由字体的大小确定。这个内容区则会生成一个行内框，如果不存在其他因素，这个行内框就完全等于该元素的内容区，由line-height产生的行间距就是增加和减少各行内框高度的因素之一。
          

**行内框 :** 行内框是一个浏览器渲染模型中的一个概念，无法显示出来，行内框默认等于内容区域， 将line-height的计算值减去font-size的计算值，这个值就是总行距，这个值可能是个负值，任何将行间距/2 分别应用到内容区的顶部和底部，其结果就是该元素的行内框。

**行框（line box）**，行框是指本行的一个虚拟的矩形框，是浏览器渲染模式中的一个概念，并没有实际显示。默认情况下行框高度等于本行内所有元素中行内框最大的值（一行上垂直对齐时以行高值最大的行内框为基准，其他行内框采用自己的对齐方式向基准对齐，最终计算行框的高度），当有多行内容时，每行都会有自己的行框。

![](line_height2.png)

# [vertical-align](https://developer.mozilla.org/zh-CN/docs/Web/CSS/vertical-align)

CSS 的属性 `vertical-align` 用来指定行内元素（inline）或表格单元格（table-cell）元素的垂直对齐方式。

>注意 vertical-align 只对行内元素、表格单元格元素生效：不能用它垂直对齐块级元素。

## 使行内元素盒模型与其行内元素容器垂直对齐。例如，用于垂直对齐一行文本的内的图片`<img>`：

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style type="text/css">
        #content {
            width: 600px;
            height: 600px;
            border: 1px red solid;
            text-align: center;
        }

        #content::after {
            content: "";
            display: inline-block;
            height: 50%;
            width: 0;
        }

        #content .icon {
            vertical-align: middle; 
        }
    </style>
</head>
<body>
    <div id="content">
        <img class="icon" src="img/img.png" alt="">
    </div>
</body>
</html>
```

# 代码

已放置于[github](https://github.com/hackycy/CSSStudyDemo)

# 参考资料

https://segmentfault.com/a/1190000009545742

https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context






