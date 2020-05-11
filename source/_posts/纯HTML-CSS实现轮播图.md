---
title: 纯HTML+CSS实现轮播图
date: 2019-06-19 14:13:12
tags:
    - HTML
categories:
    - HTML
---

一种纯HTML+CSS实现的轮播图，但由于没有JS不支持实现用户进行左右滑动。

<!-- more -->

# DOM结构搭建

``` html
<div class="slide-container middle">
    <div class="slide-wrap">
        <div class="slide">
            <img src="./1.jpg" alt="">
        </div>
        <div class="slide">
            <img src="./2.jpg" alt="">
        </div>
        <div class="slide">
            <img src="./3.jpg" alt="">
        </div>
        <div class="slide">a
            <img src="./4.jpg" alt="">
        </div>
        <div class="slide">
            <img src="./5.jpg" alt="">
        </div>
    </div>
</div>
```

``` css
.slide-container {
    width: 500px;
    height: 300px;
  	overflow: hidden;
}
.middle {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}
.slide img {
    width: 100%;
    height: 100%;
}
```

![](dom.png)

# 图片并排显示

可以看到正常文档流图片是向下排列的，如何让他们实现并排显示呢，可能会想到使用float，但是最简单的办法是使用flex布局。给`slide-wrap`容器指定为Flex布局。

``` css
.slide-wrap {
    display: flex;
    width: 500%;
    height: 100%;
}
.slide {
    width: 20%;
    height: 100%;
    transition: 1s;
}
```

![](dom2.png)

为什么`slide-wrap`要给予500%的宽度，因为该列有五张图片，一张图片刚好占满一个父容器。

# 实现点击轮播效果

不用JS如何实现轮播图切换效果呢？我们先实现轮播图的小点点。

``` css
.navigation {
    position: absolute;
    left: 50%;
    bottom: 30px;
    transform: translateX(-50%);
    display: flex;
}
.bar {
    width: 20px;
    height: 10px;
    border: 3px white solid;
    margin-left: 10px;
  	cursor: pointer;
		transition: 0.5s;
}
.bar:hover {
    background: white;
}
```

``` html
<!-- inner slide-wrap -->
<div class="navigation">
    <label class="bar" for=""></label>
    <label class="bar" for=""></label>
    <label class="bar" for=""></label>
    <label class="bar" for=""></label>
    <label class="bar" for=""></label>
</div>
```

为什么使用label来实现指示器呢，这里就关键用到了它的for属性。

``` html
<!-- inner slide-wrap -->
<input type="radio" name="r" id="r1" checked>
<input type="radio" name="r" id="r2">
<input type="radio" name="r" id="r3">
<input type="radio" name="r" id="r4">
<input type="radio" name="r" id="r5">

<div class="navigation">
    <label class="bar" for="r1"></label>
    <label class="bar" for="r2"></label>
    <label class="bar" for="r3"></label>
    <label class="bar" for="r4"></label>
    <label class="bar" for="r5"></label>
</div>
```

``` css
input[name="r"] {
    position: absolute;
    visibility: hidden;
}
```

使用label和input:radio进行关联起来后，点击的label就相当于选择了某个单选按钮，默认第一个单选按钮为选中状态，切换到第二个时，我们将第一张图向容器左边挪百分之20%的宽度(因为一共有五张图，一张图就占20%的宽度)，就可以看到切花成了第二张图，以此类推。看看代码的实现：

``` css
#r1:checked ~ .s1 {
    margin-left: 0;
}
#r2:checked ~ .s1 {
    margin-left: -20%;
}
#r3:checked ~ .s1 {
    margin-left: -40%;
}
#r4:checked ~ .s1 {
    margin-left: -60%;
}
#r5:checked ~ .s1 {
    margin-left: -80%;
}
```

点击指示器切换轮播图就完成啦。

# 自动轮播效果

``` css
.s1 {
    animation: loop 12s linear infinite;
}

@keyframes loop {
    0% {
        margin-left: 0;
    }
    15% {
        margin-left: 0;
    }
    /* 停留1500ms */
    20% {
        margin-left: -20%;
    }
    /* 切换500ms 位移-20% */
    35% {
        margin-left: -20%;
    }
    40% {
        margin-left: -40%;
    }
    55% {
        margin-left: -40%;
    }
    60% {
        margin-left: -60%;
    }
    75% {
        margin-left: -60%;
    }
    80% {
        margin-left: -80%;
    }
    95% {
        margin-left: -80%;
    }
    100% {
        margin-left: 0;
    }
    /* 复位到第一张图片 */
```

CSS3的动画属性就不过多介绍了，但是自动轮播和点击是相冲突的，但是这是一种纯CSS轮播图的一种实现思路，不需JS的实现。挺好玩的。

源码已放置GitHub：https://github.com/hackycy/Html-Css-Carousel