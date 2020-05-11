---
title: Less入门
date: 2019-04-15 23:48:32
tags:
    - Less
    - CSS
categories:
    - CSS
---

# Less是什么

LESS 将 CSS 赋予了动态语言的特性，如 变量， 继承， 运算， 函数. LESS 既可以在 客户端 上运行 (支持IE 6+, Webkit, Firefox)，也可以借助Node.js或者Rhino在服务端运行。

[Less官方文档](https://www.html.cn/doc/less/features/#mixin-guards-feature)

<!-- more  -->

# Less安装

## 客户端

引入你的`.less`样式文件的时候要设置`rel`属性值为`“stylesheet/less”`:

``` html
<link rel="stylesheet/less" type="text/css" href="styles.less">
```

下载`less.js`,[下载地址](https://www.html.cn/doc/less/),在<head> 中引入:

``` html
<script src="less.js" type="text/javascript"></script>
```

>注意你的less样式文件一定要在引入less.js前先引入。
>在浏览器中使用less.js开发是很好的，但不推荐用于生产环境中。

**特别注意**

- 确保包涵 `.less` 样式表在 `less.js` 脚本之前
- 当你引入多个 `.less` 样式表时，它们都是独立编译的。所以，在每个文件中定义的变量、混合、命名空间都不会被其它的文件共享。

## 服务端

在服务器端安装`LESS`的最简单方式就是通过`npm(node 的包管理器)`, 像这样:

``` bash
$ npm install less
```

如果你想下载最新稳定版本的 LESS，可以尝试像下面这样操作:

``` bash
$ npm install less@latest
```

### 命令行使用

一旦安装完成，就可以在命令行中调用，例如:

``` bash
$ lessc styles.less
```

这样的话编译后的CSS将会输出到 'stdout' 中，你可以选择将这个输出重定向到文件中:

``` bash
$ lessc styles.less > styles.css
```

查看版本

``` bash
$ lessc -v
$ lessc --version
```

如果你想输出一个压缩后的CSS，只要加到`-x`选项即可。如果你想要更NB的压缩，你也可以选择使用 [Clean CSS](https://github.com/GoalSmashers/clean-css)压缩器，只要加上`--clean-css` 插件即可。

直接运行lessc，不带任何参数将可以看到所有的命令行参数或 查看[用法](https://www.html.cn/doc/less/usage/index.html).

### 代码中使用

你可以在Node中调用编译器，例如：

``` javascript
var less = require('less');

less.render('.class { width: (1 + 1) }', function (e, output) {
  console.log(output.css);
});
```

将会输出

``` css
.class {
  width: 2;
}
```

>- 使用编译工具，比如 Koala 挺好用的（当然也有很多在线编译工具）
>- 在项目中使用（比如Vue，需要安装less-loader）
>- 客户端调试（存在跨域问题，不推荐这种方式）
>   + 使用link标签引用less.min.js（官网下载），注意rel="stylesheet/less"
>   + 这种方式不生成css文件，直接在浏览器查看

# 嵌套规则

## less的嵌套规则类似HTML的结构，使得CSS代码清晰

``` less
/*css 写法*/
div {
  font-size: 14px;
}
div p {
 margin: 0 auto;
}

div p a {
  color: red;
}

// less写法
div {
  font-size: 14px;
  p {
    margin: 0 auto;
    a {
      color: red;
    }
  }
}
```

## 父元素选择符 `&`

- 表示当前选择器的所有父选择器

``` less
//css写法
.bgcolor {
  background: #fff;
}
.bgcolor a {
  color: #888888;
}
.bgcolor a:hover {
  color: #ff6600;
}

//less写法
.bgcolor {
  background: #fff; 
  a {
    color: #888888;      
    &:hover {
      color: #ff6600;
    }
  }
}
```

## 改变选择器的顺序

- 将&放到当前选择器之后，会将当前选择器移到最前面
- 只需记住 “& 代表当前选择器的所有父选择器”

``` less
ul {
  li {
    .color &{
      background: #fff;
    }
  }
}

//编译结果
.color ul li {
  background: #fff;
}
```

## 组合使用

- 将生成所有可能的选择器列表

``` less
.div1, .div2 {
  color: red;
  & & {
    border-top: 1px solid blue;
  }
} 

//编译结果
.div1, .div2 {
  color: red;
}
.div1 .div2,
.div2 .div1,
.div1 .div1,
.div2 .div2 {
  border-top: 1px solid blue;
}
```

# 变量

## 变量的定义和使用

- 定义：@name: value; （@black: #000;）
- 使用场合分3种：
  + 常规使用：@name
  + 作为选择器或属性名：@{name}
  + 作为URL："@{name}"

``` less
/* 1.常规使用 */
@black: #000000;

div {
  color: @black
}

//编译结果
div {
  color: #000000;
}

/* 2.作用选择器和属性名 */
@selName: container;
@proName: width;

.@{selName} {
  @{proName}: 100px;
}

//编译结果
.container {
  width: 100px;
}

/* 3.作为URL */
@imgUrl: "./images/logo.png"

div {
  background: #FFF url("@{imgUrl}")
}

//编译结果
div {
  background: #FFF url("./images/logo.png")
}
```

## 注意事项

- 变量是延迟加载的，可以不预先声明

``` less
div {
  color: @black
}

@black: #000000;

//编译结果
div {
  color: #000000;
}
```

- 变量的作用域
  + less会从当前作用域没找到，将往上查找（类似js）
  + 如果在某级作用域找到多个相同名称的变量，使用最后定义的那个（类似css）

``` less
@var: 0;
.class {
    @var: 1;
    .brass {
        @var: 2;
        three: @var;
        @var: 3;
    }
    one: @var; //类似js，无法访问.brass内部
}

//编译结果
.class {
    one: 1;
}
.class .brass {
    three: 3;  //使用最后定义的 @var: 3
}
```

# 混合（mixins）

- 混合：一种将一系列属性从一个规则集引入（“混入”）到另一个规则集的方式
- 混合是非常重要的一个概念，内容也偏多，可以尝试多看几遍！

## 普通混合

``` less
//混合
.border {
  border-top: solid 1px black;
  border-bottom: solid 2px black;
}
#menu a {
  color: #eee;
  .border;
}

//编译结果
.border {
  border-top: solid 1px black;
  border-bottom: solid 2px black;
}

#menu a {
  color: #eee;
  border-top: solid 1px black;
  border-bottom: solid 2px black;
}
```

## 不带参数的混合

- 从上面的代码发现，混合也被编译输出了
- 在混合名字后加上括号，编译后不再输出

``` less
//加括号但不带参数的混合
.border() {
  border-top: solid 1px black;
  border-bottom: solid 2px black;
}
#menu a {
  color: #eee;
  .border;  //加不加括号都可以
}

//编译结果
#menu a {
  color: #eee;
  border-top: solid 1px black;
  border-bottom: solid 2px black;
}
```

## 带参数的混合

``` less
//带参数的混合
.border(@color) {
  border-top: solid 1px @color;
  border-bottom: solid 2px @color;
}
#menu a {
  color: #eee;
  .border(#fff);
}

//编译结果
#menu a {
  color: #eee;
  border-top: solid 1px #ffffff;
  border-bottom: solid 2px #ffffff;
}
```

## 带参数且有默认值的混合

``` less
//带参数且有默认值的混合
.border(@color: #fff) {
  border-top: solid 1px @color;
  border-bottom: solid 2px @color;
}
#menu a {
  color: #eee;
  .border;
}

#menu p {
  .border(#000);
}

//编译结果
#menu a {
  color: #eee;
  border-top: solid 1px #ffffff;
  border-bottom: solid 2px #ffffff;
}

#menu p {
  border-top: solid 1px #000000;
  border-bottom: solid 2px #000000;
}
```

## 带多个参数

- 多个参数时，参数之间可以用分号或逗号分隔
- 注意逗号分隔的是“各个参数”还是“某个列表类型的参数”
  + 两个参数，并且每个参数都是逗号分隔的列表：.name(1,2,3; something, ele)
  + 三个参数，并且每个参数都包含一个数字：.name(1,2,3)
  + 使用分号，调用包含一个逗号分割的css列表（一个参数）： .name(1,2,3; )
  + 逗号分割默认值（两个参数）：.name(@param1:red, blue)

``` less
//less编写
.mixin(@color, @padding: xxx, @margin: 2) {
  color-3: @color;
  padding-3: @padding;
  margin: @margin @margin @margin @margin;
}

.div {
  .mixin(1,2,3; something, ele);  //2个参数
}
.div1 {
  .mixin(1,2,3);                  //3个参数
}
.div2 {
  .mixin(1,2,3; );                //1个参数
}

//编译输出
.div {
  color-3: 1, 2, 3;
  padding-3: something, ele;
  margin: 2 2 2 2;
}
.div1 {
  color-3: 1;
  padding-3: 2;
  margin: 3 3 3 3;
}
.div2 {
  color-3: 1, 2, 3;
  padding-3: xxx;
  margin: 2 2 2 2;
}
```

## 定义多个相同名称的混合

- less会根据参数进行调用相应的混合

``` less
.mixin(@color) {
  color-1: @color;
}
.mixin(@color; @padding: 2) {
  color-2: @color;
  padding-2: @padding;
}
.mixin(@color; @padding: 3; @margin) {
  color-3: @color;
  padding-3: @padding;
  margin: @margin @margin @margin @margin;
}

.some .selector div {
  .mixin(#008000); //第二个mixins也被调用了，因为 @padding 有默认值
}

.some .selector p {
  .mixin(#008000, 5); //只有第二个mixins被调用
}

//编译结果
.some .selector div {
  color-1: #008000;
  color-2: #008000;
  padding-2: 2;
}
.some .selector p {
  color-2: #008000;
  padding-2: 5;
}
```

## 命名参数

- 引用mixin时可以通过参数名称而不是参数的位置来为mixin提供参数值，任何参数都通过名称来引用，这样就不必按照特定的顺序来使用参数

``` less
.mixin(@color: black; @margin: 10px; @padding: 20px) {
  color: @color;
  margin: @margin;
  padding: @padding;
}
.class1 {
  .mixin(@margin:20; @color: #33acfe);
}
.class2 {
  .mixin(#efca44; @padding: 40px);
}

//编译输出
.class1 {
  color: #33acfe;
  margin: 20px;
  padding: 20px;
}
.class2 {
  color: #efca44;
  margin: 10px;
  padding: 40px;
}
```

## @arguments变量

- @arguments表示所有可变参数
- 参数的先后顺序就是括号内的顺序 ，在赋值时，值的位置和个数也是一一对应的
- 只有一个值，把值赋给第一个，两个值，赋给第一个和第二个......
- 若想赋给第一个和第三个，必须把第二个参数的默认值写上

``` less
.border(@x: solid, @c: red) {
  border: 21px @arguments;
}
.div1 {
  .border;
}
.div2 {
  .border(solid, black)
}

//编译输出
.div1 {
  border: 21px solid #ff0000;
}
.div2 {
  border: 21px solid #000000;
}
```

## 匹配模式

- 自定义一个字符，使用时加上那个字符，就调用相应的规则

``` less
.border(all, @w: 5px) {
  border-radius: @w;
}
.border(t_l, @w: 5px) {
  border-top-left-radius: @w;
}
.border(b_l, @w: 5px) {
  border-bottom-left-radius: @w;
}
.border(b_r, @w: 5px) {
  border-bottom-right-radius: @w;
}

.border {
  .border(all, 50%);
}
//编译结果
.border {
  border-radius: 50%;
}
```

## 得到混合中变量的返回值

``` less
.average(@x, @y) {
  @average((@x + @y)/2);
}

div {
  .average(16px, 50px);
  padding: @average;
}

//编译结果
div {
  padding: 33px;
}
/*
1、将16px 和 50px 赋值给混合 .average进行计算
2、计算结果赋值给变量 @average
3、然后在div中调用@average的值
4、编译后就得到了average的值33px
*/
```

# 运算

- 任何数值、颜色值和变量都可以进行运算

## 数值类运算

- less会自动推算数值的单位，不必每个值都加上单位
- 运算符之间必须以空格分开，存在优先级问题时注意使用括号

``` less
.wp {
  width: (450px - 50)*2;
}

//编译输出
.wp {
  width: 900px;
}
```

## 颜色值运算

- 先将颜色值转换为rgb模式，运算完后再转换为16进制的颜色值并返回
- 注意：取值为0-255，所以计算时不能超过这个区间，超过默认使用0或255
- 注意：不能使用颜色名直接运算

``` less
.content {
  color: #000000 + 8;
}

//rgb(0,0,0) + 8
//rgb(8,8,8)
//十六进制：#080808

//编译输出
.content {
  color: #080808; 
}
```

# 命名空间

- 有时混合中嵌套了比较多的规则，而我们只需要其中一部分，可使用命名空间获取

## 使用 ">" 符号

``` less
//混合集
#bgcolor() {
  background: #fff;
  .a() {
    color: #888;
    &:hover {
      color: #ff6600;
    }
    .b() {
      background: #ff0000;
    }    
  }
}

.bgcolor1 {
  background: #fdfee0;
  #bgcolor>.a;     //只使用.a()
}
.bgcolor2 {
  #bgcolor>.a>.b;  //只使用.b()
}

//编译输出
.bgcolor1 {
  background: #fdfee0;
  color: #888;
}
.bgcolor1:hover {
  color: #ff6600;
}
.bgcolor2 {
  background: #ff0000;
}
```

## 省略 ">"，换成空格

``` less
//混合集
#bgcolor() {
  background: #fff;
  .a() {
    color: #888;
    &:hover {
      color: #ff6600;
    }
    .b() {
      background: #ff0000;
    }    
  }
}

.bgcolor1 {
  background: #fdfee0;
  #bgcolor .a;     //只使用.a()
}
.bgcolor2 {
  #bgcolor .a .b;  //只使用.b()
}

//编译输出
.bgcolor1 {
  background: #fdfee0;
  color: #888;
}
.bgcolor1:hover {
  color: #ff6600;
}
.bgcolor2 {
  background: #ff0000;
}
```

# 引入

- 引入一个或多个文件，这些文件定义的规则可在当前less文件中使用
- 使用@import

## 引入less文件

``` less
//main.less
@wp: 960px;
.color {
  color: #fff;
}

//当前less文件
@import "main"; //可以不加后缀
.content {
  width: @wp;
}

//编译输出
.color {
  color: #fff
}
.content {
  width: 960px;
}
```

## 引入css文件

- 注意：不能混合css的规则到项目中，编译后原样输出“@import xxx.css”
- 并且引入时不能省略后缀名

``` less
//main.css
.color {
  color: #ff6600;
}

@import "main.css" ;
.content {
  width: @wp;
  height: @wp;
}

//编译输出
@import "main.css";  //原样输出,但有效，css有这条语句
.content {
  width: 960px;
  height: 960px;
}
```

## 带参数的引入

- once：默认，只引入一次
- reference：使用less文件但不输出，注意对比上面的例子（ 不使用会输出没有加括号的混合）

``` less
@wp: 960px;
.color {
  color: #fff;
}

//当前less文件
@import (reference) "main";
.content {
  width: @wp;
}

//编译输出
.content {
  width: 960px;
}
```

- inline：在输出中包含less文件但是不能操作

``` less
@wp: 960px;
.color {
  color: #fff;
}

//当前less文件
@import (inline) "main"; 
.content {
  width: @wp;
}

//编译输出
@wp: 960px;    //报错，@wp未知
.color {
  color: #fff;
}
```

- less：将文件作为less文件对象，无论什么文件扩展名

``` less
//main.css文件
.color {
  color: #ff6600;
}

//当前less
@import (less) "main.css";
  .content {
.color;
}

//编译输出
.color {
  color: #ff6600;
}
.content {
  color: #ff6600;
}
```

- css：将文件作为css文件对象，无论什么文件扩展名

``` less
//当前less文件
@import (css) "main.less";
.content {
  color: red;
}

//编译输出
@import "main.less";
.content {
  color: red;
}
```

- multiple：允许引入多次相同文件名的文件

``` less
//当前less
@import (multiple) "main.less";
@import (multiple) "main.less";
@import (multiple) "main.less";
.content {
  width: @wp;
}

//编译输出
.color {
  color: #fff;
}
.color {
  color: #fff;
}
.color {
  color: #fff;
}
.content {
  width: 960px;
}
```

# !important

- 提升权重优先级为最高（尽量避免使用）
- 在调用的混合集后面追加 !important 关键字，混合集中所有属性都继承`!important`

``` less
.foo(@bg: #fdfdfd, @color: #900) {
  background: @bg;
  color: @color;
}

.important {
  .foo() !important
}

//编译输出
.important {
  background: #fdfdfd !important;
  color: #990000 !important;
}
```

# 条件表达式

## 带条件的混合

- 比较运算符：>, >=, =, =<, <
- 格式：when() { }

``` less
// lightness() 是检测亮度的函数，用%度量
.mixin(@a) when(lightness(@a) >= 50% ) {
  background-color: black;
}
.mixin(@a) when(lightness(@a) < 50% ) {
  background-color: white;
}

.mixin(@a) {
  color: @a;
}
.class1 {
  .mixin(#ddd);
}
.class2 {
  .mixin(#555);
}

//编译输出
.class1 {
  background-color: black;
  color: #dddddd;
}
.class2 {
  background-color: white;
  color: #555555;
}
```

## 类型检测函数

- 检测值的类型
  + iscolor
  + isnumber
  + isstring
  + iskeyword
  + isurl

``` less
.mixin(@a: #fff; @b: 0) when(isnumber(@b)) {
  color: @a;
  font-size: @b;
}
.mixin(@a; @b: black) when(iscolor(@b)) {
  font-size: @a;
  color: @b;
}
```

## 单位检测函数

- 检测一个值除了数字是否是一个特定的单位
  + ispixel
  + ispercentage
  + isem
  + isunit

``` less
.mixin(@a) when(ispixel(@a)) {
  width: @a;
}
.mixin(@a) when(ispercentage(@a)) {
  width: @a;
}

.class1 {
  .mixin(960px);
}
.class2 {
  .mixin(95%);
}

//编译输出
.class1 {
  width: 960px;
}
.class2 {
  width: 95%;
}
```

# 循环

- 混合可以调用自身，当一个混合递归调用自身就构成循环结构

``` less
.loop(@counter) when(@counter > 0) {
  .h@{counter} {
    padding: (10px*@counter);
  }
  .loop((@counter - 1)); //递归调用自身
}
div{
  .loop(5);
}

//编译输出
div .h5 {
  padding: 50px;
}
div .h4 {
  padding: 40px;
}
div .h3 {
  padding: 30px;
}
div .h2 {
  padding: 20px;
}
div .h1 {
  padding: 10px;
}
```

# 合并属性

- 将多条规则合并为一条

## 方式一

- 在需要合并的属性的冒号之前加上 “+”，合并后用逗号分隔

``` less
.mixin() {
  box-shadow+: inset 0 0 10px #555;
}
.myclass {
  .mixin;
  box-shadow+: 0 0 20px black;
}

//编译输出
.myclass {
  box-shadow: inset 0 0 10px #555, 0 0 20px black; //逗号分隔两个属性值
}
```

## 方式二

- 在需要合并的属性的冒号之前加上 “+_”，合并用空格分隔

``` less
.mixin() {
  background+_: #f66;
  background+_: url("/sss.jpg") 
}

.class {
  .mixin;
}

//编译输出
.class {
  background: #f66 url("/sss.jpg"); //空格分隔
} 
```

## 两种方式结合

``` less
.mixin() {
  background+_: #f66;
  background+: url("/sss.jpg");
  background+_: no-repeat;
  background+: center;
}
.class {
  .mixin;
}

//编译输出
.class {
  background: #f66, url("/sss.jpg")  no-repeat, center;
}
```

# 函数库

- less中封装了非常多函数库，例如颜色定义、颜色操作、颜色混合、字符串处理等等
- 例如color()：用于解析颜色，将代表颜色的字符串转换为颜色值

更多参考：https://less.bootcss.com/functions/#functions-overview

# 作用域

LESS 中的作用域跟其他编程语言非常类似，首先会从本地查找变量或者混合模块，如果没找到的话会去父级作用域中查找，直到找到为止.

``` less
@var: red;

#page {
  @var: white;
  #header {
    color: @var; // white
  }
}

#footer {
  color: @var; // red  
}
```

# 避免编译

有时候我们需要输出一些不正确的CSS语法或者使用一些 LESS不认识的专有语法.

要输出这样的值我们可以在字符串前加上一个 `~`, 例如:

``` less
.class {
  filter: ~"ms:alwaysHasItsOwnSyntax.For.Stuff()";
}

//output

.class {
  filter: ms:alwaysHasItsOwnSyntax.For.Stuff();
}
```

# 注释

- `/**/` 可以使用，会被编译
- `//` 可以使用，但不会被编译到css中，会自动过滤掉。
- `less`不认识的内容 不会被编译

# 参考链接

官方文档：https://www.html.cn/doc/less/features/

http://www.bootcss.com/p/lesscss/#guide

https://www.jianshu.com/p/15ed47ff8b06
