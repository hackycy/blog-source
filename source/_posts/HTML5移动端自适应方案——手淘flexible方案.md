---
title: HTML5移动端自适应方案——手淘flexible方案
date: 2019-05-28 16:06:59
tags:
    - HTML
    - 移动端适配
categories:
    - HTML
---

前文讲过了[HTML5移动端自适应方案——媒体查询-rem方案]，基于媒体查询方式的响应式有优点也有缺点：

优点：兼容性好，@media在ie9以上是支持的，PC和MOBILE是同一套代码的，不用分开。

缺点：要写得css相对另外两个多很多，而且各个断点都要做好。css样式会稍微大点，更麻烦。

<!-- more -->

> 本文中不涉及一些viewport、dpr、em、rem等的概念介绍，详细了解可以再查看本博客中另一篇文章有具体讲解这些概念。

所以这里介绍另一种使用Flexible实现手淘H5页面的终端适配。

# lib-flexible使用

GitHub:https://github.com/amfe/lib-flexible

[`lib-flexible`](https://github.com/amfe/lib-flexible)是一个制作H5适配的开源库，可以[点击这里](https://github.com/amfe/lib-flexible/archive/master.zip)下载相关文件，获取需要的JavaScript和CSS文件。

或者阿里CDN

> 建议使用经典版本0.3.2 [github](https://github.com/amfe/lib-flexible/tree/master)

``` html
<script src="http://g.tbcdn.cn/mtb/lib-flexible/{{version}}/??flexible_css.js,flexible.js"></script>
```

将代码中的`{{version}}`换成对应的版本号`0.3.4`。

在Web页面的`<head></head>`中添加对应的`flexible_css.js,flexible.js`文件：

``` html
<script src="build/flexible.js"></script>

//cdn
<script src="http://g.tbcdn.cn/mtb/lib-flexible/0.3.4/??flexible_css.js,flexible.js"></script>
```

另外强烈建议对JS做**内联处理**，在所有资源加载之前执行这个JS。执行这个JS后，会在`<html>`元素上增加一个`data-dpr`属性，以及一个`font-size`样式。JS会根据不同的设备添加不同的`data-dpr`值，比如说`2`或者`3`，同时会给`html`加上对应的`font-size`的值，比如说`75px`。

如此一来，页面中的元素，都可以通过`rem`单位来设置。他们会根据`html`元素的`font-size`值做相应的计算，从而实现屏幕的适配效果。

除此之外，在引入[`lib-flexible`](https://github.com/amfe/lib-flexible)需要执行的JS之前，可以手动设置`meta`来控制`dpr`值，如：

``` html
<meta name="flexible" content="initial-dpr=2" />
```

其中`initial-dpr`会把`dpr`强制设置为给定的值。如果手动设置了`dpr`之后，不管设备是多少的`dpr`，都会强制认为其`dpr`是你设置的值。**在此不建议手动强制设置`dpr`，因为在Flexible中，只对iOS设备进行`dpr`的判断，对于Android系列，始终认为其`dpr`为`1`。**

``` js
if (!dpr && !scale) {
    var isAndroid = win.navigator.appVersion.match(/android/gi);
    var isIPhone = win.navigator.appVersion.match(/iphone/gi);
    var devicePixelRatio = win.devicePixelRatio;
    if (isIPhone) {
        // iOS下，对于2和3的屏，用2倍的方案，其余的用1倍方案
        if (devicePixelRatio >= 3 && (!dpr || dpr >= 3)) {                
            dpr = 3;
        } else if (devicePixelRatio >= 2 && (!dpr || dpr >= 2)){
            dpr = 2;
        } else {
            dpr = 1;
        }
    } else {
        // 其他设备下，仍旧使用1倍的方案
        dpr = 1;
    }
    scale = 1 / dpr;
}
```

# Flexible实现自适应

图片素材：

![](designpsd.jpeg)

![](grayscale.jpeg)

![](haibao.jpg)

该设计图是按照iPhone6作为基准设计尺寸，iPhone6的分辨率为`750px * 1334px`，iPhone6的DPR为2，则CSS像素则缩小为设计稿尺寸的`1/2`。既设计稿量出某宽为20px，则css像素则转为为10px。

> 理想适口的缩放比为1的情况下

首先按照设计稿尺寸还原页面，为了方便理解，首先使用px还原页面，在iPhone6下显示设计。

实现代码：

``` html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
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

这里不做过多的页面美化。预览一下

![](preview.png)

在head中配置flexible

``` html
<script src="./node_modules/amfe-flexible/index.js"></script>
```

**把视觉稿中的px换算成rem**

之前提到设计稿是以iPhone6为基准的，即`750px * 1334px`的设计稿。而iPhone6中DPR为2，所以iPhone6下的布局视口宽度为375。

> 理想适口的缩放比为1的情况下

而Flexible的方案就是将视觉稿分成**100份**，每一份成为`a`，同时`1rem`单位被认定为`10a`，针对该设计稿可以算出

``` txt
1a = 3.75px
1rem = 37.5px
```

则我们将设计稿分成10a，则整个宽度为10rem，所以对应的`<html>`的`font-size`则为37.5px。

得到了基准值后，只需要原始的`px值`**（CSS像素）**除以`rem基准值`即可。例如一个`75px * 75px`即转换成`2rem * 2rem`。

我们统一将页面内的px值统一换算成rem，来浏览一下适配效果：

![](flexibleshipei.gif)

**如何快速换算**

- 编辑器

  在开发中总不能让自己一个一个的去计算，编辑器可以有一键转换px2rem的插件，sb3有cssrem，vscode有px to rem。

- CSS处理器，可以使用Less或者Sass实现快速转换

- PostCSS(px2rem)

  除了Sass这样的CSS处理器这外，手淘团队的大神还开发了一款`npm`的工具[px2rem](https://www.npmjs.com/package/px2rem)。安装好[px2rem](https://www.npmjs.com/package/px2rem)之后，可以在项目中直接使用。也可以使用[PostCSS](http://www.w3cplus.com/blog/tags/516.html)。使用PostCSS插件[postcss-px2rem](https://www.npmjs.com/package/postcss-px2rem)：

  ```
  var gulp = require('gulp');
  var postcss = require('gulp-postcss');
  var px2rem = require('postcss-px2rem');
  
  gulp.task('default', function() {
      var processors = [px2rem({remUnit: 75})];
      return gulp.src('./src/*.css')
          .pipe(postcss(processors))
          .pipe(gulp.dest('./dest'));
  });
  ```

  除了在Gulp中配置外，还可以使用其他的配置方式，详细的介绍可以[点击这里](https://www.npmjs.com/package/postcss-px2rem)进行了解。

  配置完成之后，在实际使用时，你只要像下面这样使用：

  ```
  .selector {
      width: 150px;
      height: 64px; /*px*/
      font-size: 28px; /*px*/
      border: 1px solid #ddd; /*no*/
  }
  ```

  `px2rem`处理之后将会变成：

  ```
  .selector {
      width: 2rem;
      border: 1px solid #ddd;
  }
  [data-dpr="1"] .selector {
      height: 32px;
      font-size: 14px;
  }
  [data-dpr="2"] .selector {
      height: 64px;
      font-size: 28px;
  }
  [data-dpr="3"] .selector {
      height: 96px;
      font-size: 42px;
  }
  ```

  在整个开发中有了这些工具之后，完全不用担心`px`值转`rem`值影响开发效率。

# 文本字号不建议使用rem

前面大家都见证了如何使用`rem`来完成H5适配。那么文本又将如何处理适配。是不是也通过`rem`来做自动适配。

显然，我们在iPhone3G和iPhone4的Retina屏下面，希望看到的文本字号是相同的。也就是说，我们**不希望文本在Retina屏幕下变小**，另外，我们**希望在大屏手机上看到更多文本**，以及，现在绝大多数的字体文件都自带一些点阵尺寸，通常是`16px`和`24px`，所以我们**不希望出现13px和15px这样的奇葩尺寸**。

如此一来，就决定了在制作H5的页面中，`rem`并不适合用到段落文本上。所以在Flexible整个适配方案中，考虑文本还是使用`px`作为单位。只不过使用`[data-dpr]`属性来区分不同`dpr`下的文本字号大小。

```css
div {
    width: 1rem; 
    height: 0.4rem;
    font-size: 12px; // 默认写上dpr为1的fontSize
}
[data-dpr="2"] div {
    font-size: 24px;
}
[data-dpr="3"] div {
    font-size: 36px;
}
```

为了能更好的利于开发，在实际开发中，我们可以定制一个[`font-dpr()`](https://github.com/W3cplus/Sass-Resources/blob/master/mixins/_font-dpr.scss)这样的Sass混合宏：

```scss
@mixin font-dpr($font-size){
    font-size: $font-size;

    [data-dpr="2"] & {
        font-size: $font-size * 2;
    }

    [data-dpr="3"] & {
        font-size: $font-size * 3;
    }
}
```

有了这样的混合宏之后，在开发中可以直接这样使用：

```scss
@include font-dpr(16px);
```

当然这只是针对于描述性的文本，比如说段落文本。但有的时候文本的字号也需要分场景的，比如在项目中有一个slogan,业务方希望这个slogan能根据不同的终端适配。针对这样的场景，完全可以使用`rem`给slogan做计量单位。

> 注意，如果需要该功能版本的请使用0.3.2版本，既主分支版本或者使用cdn版本

# Flexible原理

flexible的核心代码很简单：

``` js
var docEl = document.documentElement  
// set 1rem = viewWidth / 10
function setRemUnit () {
  var rem = docEl.clientWidth / 10
  docEl.style.fontSize = rem + 'px'
}

setRemUnit()
```

上面的代码中，将`html`节点的`font-size`设置为页面`clientWidth`(布局视口)的`1/10`，即`1rem`就等于页面布局视口的`1/10`，所以这就是为什么上面所说的px换算rem的代码实现。

里面还有一些监听页面大小变化，布局可以自适应:

``` js
 // reset rem unit on page resize
  window.addEventListener('resize', setRemUnit)
  window.addEventListener('pageshow', function (e) {
    if (e.persisted) {
      setRemUnit()
    }
  })
```

以及设置data-dpr

``` javascript
docEl.setAttribute('data-dpr', dpr);
```

> 完整源码请查看主分支版本中的js
>
> 由于`viewport`单位得到众多浏览器的兼容，lib-flexible这个过渡方案已经被官方弃用，但是其原理还是需要去理解的。因为vh、vw的方案原理都是大致相同的。