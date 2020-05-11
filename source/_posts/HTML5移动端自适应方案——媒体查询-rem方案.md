---
title: HTML5移动端自适应方案——媒体查询+rem方案
date: 2019-04-22 16:48:42
tags:
    - HTML
    - 移动端适配
categories:
    - HTML
---

# 背景

虽然H5的页面与PC的Web页面相比简单了不少，但让我们头痛的事情是要想尽办法让页面能适配众多不同的终端设备。

<!-- more -->

>本文中不涉及一些viewport、dpr等的概念介绍，详细了解可以再查看本博客中另一篇文章有具体讲解这些概念。

# EM和REM

使用REM+媒体查询方式进行编写自适应方案时，先来了解下EM和REM是什么？

## EM

em是相对长度单位。相对于当前对象内文本的字体尺寸。如当前对行内文本的字体尺寸未被人为设置，则相对于浏览器的默认字体尺寸。

**EM的特点**

- em的值并不是固定的
- em会继承父级元素的字体大小

>注意：任意浏览器的默认字体高都是16px。所有未经调整的浏览器都符合: 1em=16px。那么12px=0.75em,10px=0.625em。为了简化font-size的换算，需要在css中的body选择器中声明Font-size=62.5%，这就使em值变为 16px*62.5%=10px, 这样12px=1.2em, 10px=1em, 也就是说只需要将你的原来的px数值除以10，然后换上em作为单位就行了。
>所以我们在写CSS的时候，需要注意两点：
>1. body选择器中声明Font-size=62.5%；
>2. 将你的原来的px数值除以10，然后换上em作为单位；
>3. 重新计算那些被放大的字体的em数值。避免字体大小的重复声明。
>也就是避免1.2 * 1.2= 1.44的现象。比如说你在#content中声明了字体大小为1.2em，那么在声明p的字体大小时就只能是1em，而不是1.2em, 因为此em非彼em，它因继承#content的字体高而变为了1em=12px。

## REM 

`rem`是`CSS3`新增的一个相对单位（root em，根em），这个单位引起了广泛关注。这个单位与em有什么区别呢？区别在于使用rem为元素设定字体大小时，仍然是相对大小，但相对的只是HTML根元素。这个单位可谓集相对大小和绝对大小的优点于一身，通过它既可以做到只修改根元素就成比例地调整所有字体大小，又可以避免字体大小逐层复合的连锁反应。目前，除了IE8及更早版本外，所有浏览器均已支持rem。

简单的理解，rem就是相对于根元素`<html>`的`font-size`来做计算。而我们的方案中使用rem单位，是能轻易的根据`<html>`的`font-size`计算出元素的盒模型大小。而这个特色对我们来说是特别的有益处。

# 媒体查询 + Rem实现自适应

放上代码中所使用的设计稿和图片，自行右键保存。图片来源[手淘H5适配方案](https://github.com/amfe/article/issues/17)

![](designpsd.jpeg)

![](grayscale.jpeg)

![](haibao.jpg)

该设计图是按照iPhone6作为基准设计尺寸，iPhone6的分辨率为`750px * 1334px`，iPhone6的DPR为2，则CSS像素则缩小为设计稿尺寸的`1/2`。既设计稿量出某宽为20px，则css像素则转为为10px。

> 理想适口的缩放比为1的情况下

首先按照设计稿尺寸还原页面，为了方便理解，首先使用px还原页面，在iPhone6下显示设计。

``` html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        * {
            padding: 0;
            margin: 0;
            box-sizing: border-box;
            -webkit-tap-highlight-color: transparent;
        }

        html,body {
            height: 100%;
        }

        html,body {
            height: 100%;
            background-color: #f5294c;
        }

        body {
            font-size: 14px;
        }

        #content {
            /* background-color: #f5294c; */
            min-width: 100%;
        }

        #content .header h1 img {
            width: 100%;
        }

        #content .section {
            padding: 0 5px;
        }

        #content .section .item {
            background-color: #fff;
            position: relative;
        }

        #content .section .item .left {
            padding: 5px 0;
            display: table-cell;
        }

        #content .section .item .left img{
            width: 88px;
            height: 88px;
        }

        #content .section .item .right {
            display: table-cell;
            padding: 5px 8px 0 8px;
            vertical-align: top;
            width: 100%;
            font-size: 13px;
            line-height: 1.25;
        }

        #content .section .item .right .price {
            margin: 9px 0;
        }

        #content .section .item .right .price {
            color: #f32a4c;
        }

        #content .section .item .right .price span {
            font-size: 10px;
            background-color: #f32a4c;
            color: #fffffd;
        }

        #content .section .item .right .intro {
            color: #ffb09b;
        }

        #content .section .item .buy {
            position: absolute;
            right: 9px;
            bottom: 7px;
            line-height: 1.25;
            font-size: 13px;
            padding: 5px 18px;
            background-color: #f42a4b;
            color: #fefffc;
        }

        #content .section .item .buy a:visited {
            color: #fefffc;
        }

        
    </style>
</head>

<body>
    <div id="content">
        <!-- 海报 -->
        <div class="header">
            <h1>
                <img src="image/haibao.jpg" alt="" srcset="">
            </h1>
        </div>
        <ul class="section">
            <li class="item">
                <a class="left" href="##">
                    <img src="image/grayscale.jpeg" alt="" srcset="">
                </a>
                <div class="right">
                    <div class="title">
                        <a href="##">Carter's1年式灰色长袖连体衣包脚爬服全棉鲸鱼男婴儿童装115G093</a>
                    </div>
                    <div class="price">
                        <span>双11价</span>
                        <strong>¥ 299</strong>
                        <small>（满400 减 100）</small>
                    </div>
                    <div class="intro">
                        <small>一小时热卖1769件</small>
                    </div>
                </div>
                <div class="buy">
                    <a href="##">马上抢！</a>
                </div>
            </li>
            <li class="item">
                <a class="left" href="##">
                    <img src="image/grayscale.jpeg" alt="" srcset="">
                </a>
                <div class="right">
                    <div class="title">
                        <a href="##">Carter's1年式灰色长袖连体衣包脚爬服全棉鲸鱼男婴儿童装115G093</a>
                    </div>
                    <div class="price">
                        <span>双11价</span>
                        <strong>¥ 299</strong>
                        <small>（满400 减 100）</small>
                    </div>
                    <div class="intro">
                        <small>一小时热卖1769件</small>
                    </div>
                </div>
                <div class="buy">
                    <a href="##">马上抢！</a>
                </div>
            </li>
            <li class="item">
                <a class="left" href="##">
                    <img src="image/grayscale.jpeg" alt="" srcset="">
                </a>
                <div class="right">
                    <div class="title">
                        <a href="##">Carter's1年式灰色长袖连体衣包脚爬服全棉鲸鱼男婴儿童装115G093</a>
                    </div>
                    <div class="price">
                        <span>双11价</span>
                        <strong>¥ 299</strong>
                        <small>（满400 减 100）</small>
                    </div>
                    <div class="intro">
                        <small>一小时热卖1769件</small>
                    </div>
                </div>
                <div class="buy">
                    <a href="##">马上抢！</a>
                </div>
            </li>
            <li class="item">
                <a class="left" href="##">
                    <img src="image/grayscale.jpeg" alt="" srcset="">
                </a>
                <div class="right">
                    <div class="title">
                        <a href="##">Carter's1年式灰色长袖连体衣包脚爬服全棉鲸鱼男婴儿童装115G093</a>
                    </div>
                    <div class="price">
                        <span>双11价</span>
                        <strong>¥ 299</strong>
                        <small>（满400 减 100）</small>
                    </div>
                    <div class="intro">
                        <small>一小时热卖1769件</small>
                    </div>
                </div>
                <div class="buy">
                    <a href="##">马上抢！</a>
                </div>
            </li>
            <li class="item">
                <a class="left" href="##">
                    <img src="image/grayscale.jpeg" alt="" srcset="">
                </a>
                <div class="right">
                    <div class="title">
                        <a href="##">Carter's1年式灰色长袖连体衣包脚爬服全棉鲸鱼男婴儿童装115G093</a>
                    </div>
                    <div class="price">
                        <span>双11价</span>
                        <strong>¥ 299</strong>
                        <small>（满400 减 100）</small>
                    </div>
                    <div class="intro">
                        <small>一小时热卖1769件</small>
                    </div>
                </div>
                <div class="buy">
                    <a href="##">马上抢！</a>
                </div>
            </li>
        </ul>
    </div>
</body>

</html>
```

编写后页面显示，当然这里是举例，有些像素为了方便计算，只是大概的测量了一下，这里使用的测量工具为`Mark Man`。

![](px_h5.gif)

然后看看其他设备显示的效果，因为这里使用的是`流式布局`，即百分比布局，页面还没有说变形很严重，但是在iPhone5设备上或者更小的设备上显示就会显得页面元素偏大，而在一些ipad就显得更为难受了。(换成其他DPR下情况也是相同)。

这里不讲解媒体查询是什么和具体语法，详细了解可以查看[MDN](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Media_queries)

而该方案就是基于rem的原理，针对不同屏幕尺寸的改变来改变根节点html的`font-size`的大小。

这里总结出了一个公式：

``` txt
font-size(rem) = 预设font-size(rem)基准值 / （设计稿宽度 / DPR） * 设备宽度
```

该公式的原理就是先根据设计稿的尺寸和DPR求出设备的宽度，根据该设备的宽度下预设一个`font-size`的基准值，再求出该设备下的宽度与预设基准值的比值，然后其他设备下的设备宽度除以该基准值就可以求出其他设备下的html的`font-size`的值。所以上述公式是由下条公式变化得来的。

``` txt
font-size(rem) = 设备宽度 / （（设计稿宽度 / DPR） / 预设font-size(rem)基准值）
```

按照本文中设计稿为iPhone6的尺寸设计，我们以设计稿下iPhone6为`375px`的html的`font-size`预设基准值为`100px`（方便计算）,即`1rem = 100px`，则其他设备像素下html的`font-size`的值为

- iPhone5的设备像素320px:`100 / (750 / 2) * 320 = 85.333px`
- iPhone6的设备像素375px:`100px`
- iPhone6Plus的设备像素414px:`100 / (750 / 2) * 414 = 110.4px`

只要以设计稿下预设的基准值再根据其他屏幕来换算计算出其他设备下的html的`font-size`大小即可。

页面中再根据`1rem = 100px`将页面中px单位换算成rem即可。再添加上根据设备宽度的媒体查询设置根节点的`font-size`大小。

``` css
/* base-rem = 100px */

        @media (min-width: 320px) {
            html {
                font-size: 85.333px;
            }
        }

        @media (min-width: 360px) {
            html {
                font-size: 96px;
            }
        }

        @media (min-width: 375px) {
            html {
                font-size: 100px;
            }
        }

        @media (min-width: 414px) {
            html {
                font-size: 110.4px;
            }
        }

        @media (min-width: 480px) {
            html {
                font-size: 128px;
            }
        }

        @media (min-width: 540px) {
            html {
                font-size: 144px;
            }
        }

        @media (min-width: 768px) {
            html {
                font-size: 204.8px;
            }
        }
```

更改后整体页面代码

``` html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0,user-scalable=no">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        * {
            padding: 0;
            margin: 0;
            box-sizing: border-box;
        }

        html,body {
            height: 100%;
        }

        /* base-rem = 100px */

        @media (min-width: 320px) {
            html {
                font-size: 85.333px;
            }
        }

        @media (min-width: 360px) {
            html {
                font-size: 96px;
            }
        }

        @media (min-width: 375px) {
            html {
                font-size: 100px;
            }
        }

        @media (min-width: 414px) {
            html {
                font-size: 110.4px;
            }
        }

        @media (min-width: 480px) {
            html {
                font-size: 128px;
            }
        }

        @media (min-width: 540px) {
            html {
                font-size: 144px;
            }
        }

        @media (min-width: 768px) {
            html {
                font-size: 204.8px;
            }
        }

        html,body {
            height: 100%;
            background-color: #f5294c;
        }

        body {
            font-size: 14px;
        }

        #content {
            /* background-color: #f5294c; */
            min-width: 100%;
        }

        #content .header h1 img {
            width: 100%;
        }

        #content .section {
            padding: 0 0.05rem;
        }

        #content .section .item {
            background-color: #fff;
            position: relative;
        }

        #content .section .item .left {
            padding: 0.05rem 0;
            display: table-cell;
        }

        #content .section .item .left img{
            width: 0.88rem;
            height: 0.88rem;
        }

        #content .section .item .right {
            display: table-cell;
            padding: 0.05rem 0.08rem 0 0.08rem;
            vertical-align: top;
            width: 100%;
            font-size: 0.13rem;
            line-height: 1.25;
        }

        #content .section .item .right .price {
            margin: 0.09rem 0;
        }

        #content .section .item .right .price {
            color: #f32a4c;
        }

        #content .section .item .right .price span {
            font-size: 0.1rem;
            background-color: #f32a4c;
            color: #fffffd;
        }

        #content .section .item .right .intro {
            color: #ffb09b;
        }

        #content .section .item .buy {
            position: absolute;
            right: 0.09rem;
            bottom: 0.07rem;
            line-height: 1.25;
            font-size: 0.13rem;
            padding: 0.05rem 0.18rem;
            background-color: #f42a4b;
            color: #fefffc;
        }

        #content .section .item .buy a:visited {
            color: #fefffc;
        }

        
    </style>
</head>

<body>
    <div id="content">
        <!-- 海报 -->
        <div class="header">
            <h1>
                <img src="image/haibao.jpg" alt="" srcset="">
            </h1>
        </div>
        <ul class="section">
            <li class="item">
                <a class="left" href="##">
                    <img src="image/grayscale.jpeg" alt="" srcset="">
                </a>
                <div class="right">
                    <div class="title">
                        <a href="##">Carter's1年式灰色长袖连体衣包脚爬服全棉鲸鱼男婴儿童装115G093</a>
                    </div>
                    <div class="price">
                        <span>双11价</span>
                        <strong>¥ 299</strong>
                        <small>（满400 减 100）</small>
                    </div>
                    <div class="intro">
                        <small>一小时热卖1769件</small>
                    </div>
                </div>
                <div class="buy">
                    <a href="##">马上抢！</a>
                </div>
            </li>
            <li class="item">
                <a class="left" href="##">
                    <img src="image/grayscale.jpeg" alt="" srcset="">
                </a>
                <div class="right">
                    <div class="title">
                        <a href="##">Carter's1年式灰色长袖连体衣包脚爬服全棉鲸鱼男婴儿童装115G093</a>
                    </div>
                    <div class="price">
                        <span>双11价</span>
                        <strong>¥ 299</strong>
                        <small>（满400 减 100）</small>
                    </div>
                    <div class="intro">
                        <small>一小时热卖1769件</small>
                    </div>
                </div>
                <div class="buy">
                    <a href="##">马上抢！</a>
                </div>
            </li>
            <li class="item">
                <a class="left" href="##">
                    <img src="image/grayscale.jpeg" alt="" srcset="">
                </a>
                <div class="right">
                    <div class="title">
                        <a href="##">Carter's1年式灰色长袖连体衣包脚爬服全棉鲸鱼男婴儿童装115G093</a>
                    </div>
                    <div class="price">
                        <span>双11价</span>
                        <strong>¥ 299</strong>
                        <small>（满400 减 100）</small>
                    </div>
                    <div class="intro">
                        <small>一小时热卖1769件</small>
                    </div>
                </div>
                <div class="buy">
                    <a href="##">马上抢！</a>
                </div>
            </li>
            <li class="item">
                <a class="left" href="##">
                    <img src="image/grayscale.jpeg" alt="" srcset="">
                </a>
                <div class="right">
                    <div class="title">
                        <a href="##">Carter's1年式灰色长袖连体衣包脚爬服全棉鲸鱼男婴儿童装115G093</a>
                    </div>
                    <div class="price">
                        <span>双11价</span>
                        <strong>¥ 299</strong>
                        <small>（满400 减 100）</small>
                    </div>
                    <div class="intro">
                        <small>一小时热卖1769件</small>
                    </div>
                </div>
                <div class="buy">
                    <a href="##">马上抢！</a>
                </div>
            </li>
            <li class="item">
                <a class="left" href="##">
                    <img src="image/grayscale.jpeg" alt="" srcset="">
                </a>
                <div class="right">
                    <div class="title">
                        <a href="##">Carter's1年式灰色长袖连体衣包脚爬服全棉鲸鱼男婴儿童装115G093</a>
                    </div>
                    <div class="price">
                        <span>双11价</span>
                        <strong>¥ 299</strong>
                        <small>（满400 减 100）</small>
                    </div>
                    <div class="intro">
                        <small>一小时热卖1769件</small>
                    </div>
                </div>
                <div class="buy">
                    <a href="##">马上抢！</a>
                </div>
            </li>
        </ul>
    </div>
</body>

</html>
```

适配后

![gif](rem_h5_dpr2.gif)

图片较大，可自行运行代码在dpr为1、2、3下再更改屏幕尺寸，运行结果良好，可谓够用。

为什么可谓够用，该方案有缺点：通过设备宽度范围区间这样的媒体查询来动态改变rem基准值，其实不够精确，比如：宽度为360px 和 宽度为320px的手机，因为屏宽在同一范围区间内(<375px)，所以会被同等对待(rem基准值相同)，而事实上他们的屏幕宽度并不相等，它们的布局也应该有所不同。最终，结论就是：这样的做法，没有做到足够的精确，但是够用。

# Less优化

如果项目中使用Less或者Sass等CSS预处理器可以更加简化代码的编写与计算量。

``` less
//需要适配的设备宽度数组
@adapterDeviceWidthList: 320px, 360px, 375px, 414px, 480px, 540px, 640px, 750px;

@psdWidth: 750;

@psdDpr: 2;

@baseFontSize: 100px;

@deviceLength: length(@adapterDeviceWidthList);

.adapterMixin(@index) when (@index <= @deviceLength) {
    @media (min-width: extract(@adapterDeviceWidthList, @index)) {
        html {
            font-size: 100 / (@psdWidth / @psdDpr) * extract(@adapterDeviceWidthList, @index);
        }
    }
    .adapterMixin(@index + 1);
}

.adapterMixin(1);
```

生成的css为

``` css
@media (min-width: 320px) {
  html {
    font-size: 85.33333333px;
  }
}
@media (min-width: 360px) {
  html {
    font-size: 96px;
  }
}
@media (min-width: 375px) {
  html {
    font-size: 100px;
  }
}
@media (min-width: 414px) {
  html {
    font-size: 110.4px;
  }
}
@media (min-width: 480px) {
  html {
    font-size: 128px;
  }
}
@media (min-width: 540px) {
  html {
    font-size: 144px;
  }
}
@media (min-width: 640px) {
  html {
    font-size: 170.66666667px;
  }
}
@media (min-width: 750px) {
  html {
    font-size: 200px;
  }
}
```

使用less维护起来就方便很多，只要维护`adapterDeviceWidthList`中设备的宽度即可，如果设计稿开始有变化更改变量即可。




