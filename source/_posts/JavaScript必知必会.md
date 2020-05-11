---
title: JavaScript必知必会
date: 2019-05-29 22:10:12
tags:
    - JavaScript
categories:
    - JavaScript
---

# JavaScript基本介绍

+ JS的用途：Javascript可以实现浏览器端、服务器端(nodejs)。。。
+ 浏览器端JS由以下三个部分组成：
    - ECMAScript：基础语法(数据类型、运算符、函数。。。)
    
    - BOM(浏览器对象模型)：window、location、history、navigator。。。

    - DOM(文档对象模型)：div、p、span。。。
    

<!-- more -->

+ ECMAScript又名es，有以下重大版本：
    - 旧时代：
        - es1.0。。。es3.1
    - 新时代：
        - es5
        - es6(es2015)
        - es7(es2016)、es8(es2017)

# JavaScript基本数据类型和复杂数据类型

一句总结，使用排除法，除掉基本数据类型都是复杂数据类型即`字符串`、`数字`、`布尔值`、`null`、`undefined`、`Symbol`其余都是对象类型。复杂数据类型例如`Date`、`Array`等。

# 对象的基本使用

``` html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title></title>
    </head>
    <body>
        <script type="text/javascript">
            //创建对象
            var person = {
                    name : "张三",
                    age : 18,
                    say : function(){
                        console.log('i am from china');
                    }
            };
            
            //获取属性
            console.log(person.name);
            console.log(person.age);
            person.say();
            
            //设置属性
            person.address = "中国广东广州";
            console.log(person.address);
            
            //清除属性
            delete person.address;
            delete person.adc  //无意义,但是也不会报错
            console.log(person.address);
            
            //delete只能删除对象的属性,而不能删除变量
            
            
            //清空对象
            person={};
            //将person变为null
            person = null;
            //两者意义不一样
            
        </script>
    </body>
</html>

```
输出
``` console
张三
18
i am from china
```

对象是键值对的集合：对象是由属性和方法构成的 (ps：也有说法为：对象里面皆属性，认为方法也是一个属性)
+ name是属性    age是属性
+ say是方法

## 获取属性的方式

### .语法

+ `person.age`获取age属性
+ `person.say`获取到一个方法

### []语法

+ `person["name"]`等价于person.age
+ `person["say"]`等价于person.say

### 2种方式的差异：

+ .语法更方便，但是坑比较多(有局限性)，比如：
    - .后面不能使用js中的关键字、保留字(class、this、function。。。)
    - .后面不能使用数字
```js
    var obj={};
    obj.this=5; //语法错误
    obj.0=10;   //语法错误
```

+ []使用更广泛
    - o1[变量name]
    - ["class"]、["this"]都可以随意使用 `obj["this"]=10`
    - [0]、[1]、[2]也可以使用       
        - `obj[3]=50 = obj["3"]=50`     
        - 思考：为什么obj[3]=obj["3"]
    - 甚至还可以这样用：["[object Array]"]
        - jquery里面就有这样的实现
    - 也可以这样用：["{abc}"]
        - 给对象添加了{abc}属性

## 设置属性
+ `student["gender"]="男"`    等价于：    `student.gender="男"`
    - 含义：如果student对象中没有gender属性，就添加一个gender属性，值为"男"
    -      如果student对象中有gender属性，就修改gender属性的值为"男"
+ 案例1：`student.isFemale=true`
+ 案例2：`student["children"]=[1,2,5]`
+ 案例3：
```js
    student.toShanghai=function(){   
        console.log("正在去往上海的路上")   
    }
```

## 删除属性
+ `delete student["gender"]`      
+ `delete student.gender`

## 属性简写

``` js
var a = 10;

var obj = { a }; // { a: 10 }
```

# 构造函数

## 概念
``` js
<script type="text/javascript">
            function Person(name, age){
                this.name = name;
                this.age = age;
            }
            var person = new Person('张三', 40);
            console.log(person);
</script>
```
person就是根据【Person构造函数】创建出来的对象

>任何函数都可以当成构造函数

例如`function CreateFunc(){ }`

只要把一个函数通过new的方式来进行调用，我们就把这一次函数的调用方式称之为：构造函数的调用
- new CreateFunc(); 此时CreateFunc就是一个构造函数
- CreateFunc();     此时的CreateFunc并不是构造函数

关于new Object()，new Object()等同于对象字面量{}

## 构造函数的执行过程

`var person = new Person()`
+ 创建一个对象，称之为这个Person构造函数的实例，`p1`
+ 创建一个内部对象，`this`，将`this`指向该实例
+ 执行函数内部的代码，其中，操作`this`的部分就是操作了该实例
+ 返回值
    - 如果函数没有返回值，没有`return`语句，那么就会返回构造函数的实例`p1`
    - 如果函数返回了一个基本数据类型的值，那么就会返回本次构造函数的实例`p1`
    - 如果函数返回了一个复杂数据类型的值，那么本次函数的返回值就是该值。

``` js
        function fn(){
            
        }
        var f1=new fn();    //f1就是fn的实例

        function fn2(){
            return "abc";
        }
        var f2=new fn2();   //f2是fn2构造函数的实例

        function fn3(){
            return [1,3,5]; 
            //数组是一个对象类型的值，
            //所以数组是一个复杂数据类型的值
            //-->本次构造函数的真正返回值就是该数组
            //-->不再是fn3构造函数的实例
        }
        var f3=new fn3();   //f3还是fn3的实例吗？错
        //f3值为[1,3,5]
```

# JS中的继承

JS中的继承和其他语言不太一样。JS中的继承是你可以通过某种方式让某个对象访问到其他对象中的属性、方法，那么这种方式就可以称之为继承。而并不是简单的所谓的`Xxx extends Parent`

## 为什么要使用继承

+ 有些对象会有方法(动作、行为)，而这些方法都是函数，如果把这些方法和函数都放在构造函数中声明就会导致内存的浪费
```js
    function Person(){
        this.say=function(){
            console.log("你好")
        }
    }
    var p1=new Person();
    var p2=new Person();
    console.log(p1.say === p2.say);   //false
```

## 继承的方式

### 原型链继承

#### 原型链继承第一种方式
``` js
Person.prototype.say = function(){
	console.log('hello');
}
```
当然这样写没有问题，但是方法一旦过多，代码的冗余将会非常的多。

#### 原型链继承第二种方式

``` js
Person.prototype = {
	constructor:Person,
	say:function(){
		console.log('hello');
	},
	run:function(){
		console.log('running');
	}
}
```
>注意：
>1、一般情况下，应该先改变原型对象，再创建对象。
>2、对于新原型，一般会添加一个constructor属性，从而不破坏原来的原型对象结构。

### 拷贝继承（也称混入继承：mixin）

场景：有时候想使用某个对象中的属性，但是又不想直接破坏它，于是就可以创建这个对象的拷贝
类似于jQuery中的`$.extend`，编写Jquery插件的必经之路

来看一个例子
``` js
var o1 = {
    age : 1
}

var o2 = o1;

o2.age = 18;

console.log(o1.age); //结果输出18

//1、修改了o2对象的age属性
//2、由于o2对象跟o1对象是同一个对象
//3、所以此时o1对象的age属性也被修改了
```
这里涉及到一个知识点`深拷贝和浅拷贝`，
- 深拷贝只是拷贝一层属性，没有内部对象
- 深拷贝其实是利用了递归的原理，将对象的若干层属性拷贝出来。
- 上述例子中只是一个浅拷贝。

*那么什么场景下适合使用拷贝继承*
``` js
var o3 = {
    gender : '男',
    grade : '初三',
    name : '张三'
}
var o4 = {
    gender : '男',
    grade : '初三',
    name : '李四'
}
```

- 实现1
``` js
var o3 = {
    gender : '男',
    grade : '初三',
    name : '张三'
}
var cp = {};
cp.gender = o3.gender;
cp.grade = o3.gender;
cp.name = o3.name;

console.log(cp); //{gender: "男", grade: "男", name: "张三"}
```
这样的方式可以说在开发中就是写死的，毫无重用性。

- 实现2
``` js
var o3 = {
    gender : '男',
    grade : '初三',
    name : '张三'
}
function extend(target,source){
    for(key in source) {
        target[key] = source[key];
    }
    return target;
}
var target = {};
extend(target, o3);
console.log(target); //{gender: "男", grade: "初三", name: "张三"}
```
如果很多时候需要用到拷贝继承，无疑封装成一个函数来进行复用是很好的。

在es6中也有了`对象扩展运算符`，仿佛就是为了拷贝继承而生的:
``` js
var o3 = {
    gender : '男',
    grade : '初三',
    name : '张三'
}
var target = { ...o3 };
console.log(target);//{gender: "男", grade: "初三", name: "张三"}
var target2 = { ...o3, age:20 };
console.log(target2);//{gender: "男", grade: "初三", name: "张三", age: 20}
```
可以说简单到令人发指的操作，当然也会存在兼容问题啦，这里不涉及es6，单纯扯一下。

### 原型式继承（道格拉斯在蝴蝶书中提出的）

+ 使用场景
    - 可以创建一个纯洁的对象，对象中什么属性都没有
    - 创建一个继承自某个父对象的字对象

``` js
//创建空对象
var o1 = Object.create(null);
console.log(o1); //{}
console.log(o1.__proto__) //undefined

//继承父对象
var parent = {
	age : 50,
	name : "爸爸"
}
var s1 = Object.create(parent);
console.log(s1); //{}
console.log(s1.__proto__); //{age: 50, name: "爸爸"}
```

可以看得出一些区别，所谓的纯洁对象，真的很纯。这个使用也很方便。

### 借用构造函数继承

场景：适用于2种构造函数之间有相似的逻辑的情况。
原理：函数的call、apply调用方式（函数的调用方式在文章后面讲解）

场景举例：
``` js
function Animal(name,age,gender){
    this.name=name;
    this.age=age;
    this.gender=gender;
}
function Person(name,age,gender,say){
    this.name=name;
    this.age=age;
    this.gender=gender;

    this.say=function(){

    }
}
```
>局限性：`Animal`（父类的构造函数）的构造函数必须完全适用于`Person`（子类的构造函数）

借用构造函数实现
``` js
function Animal(name,age){
            this.name=name;
            this.age=age;
}
function Person(name,age,say){
            // this.name=name;
            // this.age=age;
            // this.gender=gender;
    Animal.apply(this, [name, age]); 
            //等同与下面语句
            //Animal.call(this, name, age);

    this.say=function(){

    }
}

console.log(new Person('张三', 18, function(){
    console.log('say');
})) //{name: "张三", age: 18, say: ƒ}
```

### and so on
还有寄生继承，寄生组合继承，这里举例出以上几条常用的，其余这里就不再一一细数了。

# 原型链

概念：JS中的对象可能会有父对象，父对象中可能还会有父对象，就是祖宗十九代了。
根本：继承
    - 属性：对象中几乎都会有一个`__proto__`的一个属性，指向他的父对象
    - 意义，可以访问到父对象中的相关属性和方法
根对象：`Object.prototype`

``` js
function Animal(){}
var cat = new Animal();
console.log(cat.__proto__); 
//等同于 Animal.prototype
console.log(cat.__proto__.__proto__);
//等同于 Object.prototype
```

>函数对象都有`prototype`（原型对象）；而普通对象则只有`__proto__`（原型指针）

# 变量作用域

概念：就是一个变量可以使用的范围。

- 最外层作用域：全局作用域
- 通过函数创建出一个独立的作用域，其中函数还可以嵌套，所以作用域也可以嵌套。

## 作用域链

+ 由于作用域是相对于变量而言的，而如果存在多级作用域，这个变量又来自于哪里？这个问题就需要好好地探究一下了，我们把这个变量的查找过程称之为变量的作用域链
+ 作用域链的意义：查找变量（确定变量来自于哪里，变量是否可以访问）
+ 简单来说，作用域链可以用以下几句话来概括：(或者说：确定一个变量来自于哪个作用域)
    - 查看当前作用域，如果当前作用域声明了这个变量，就确定结果
    - 查找当前作用域的上级作用域，也就是当前函数的上级函数，看看上级函数中有没有声明
    - 再查找上级函数的上级函数，直到全局作用域为止
    - 如果全局作用域中也没有，我们就认为这个变量未声明(xxx is not defined)

例子1：
``` js
var age = 10; //全局变量

(function log(){
	console.log(age); //可访问

	var name = "zhangsan";

	console.log(name); //可访问

})();

console.log(name); //不能访问

console.log(age); //可访问
```

例子2：
``` js
var gender  = "男";

var f = (function(){
    console.log(gender); //可以访问
    return function() {
        console.log(gender); //可以访问
    }
    var age = 10;
})();
f();
```

# 闭包

一个经典的例子
``` js
function fn(){
    var a = 1;
    return function(){
        a++;
        console.log(a);
    }
}
var f = fn();
f(); //2
f(); //3
f(); //4
```

可以看出a的输出并没有一直输出3，而是2、3、4，而产生的原因是因为`fn`函数执行完毕后，匿名函数的引用导致没有释放a变量，而作用域中保留了最新的a变量的值，闭包问题就产生了。

>闭包不仅仅只是说要返回一个函数，还可以是一个对象

## 闭包的内存释放

``` javascript
<script>
    function f1(){
        var a=5;
        return function(){
            a++;
            console.log(a);
        }
    }
    var q1=f1();
    
    //要想释放q1里面保存的a，只能通过释放q1
    q1=null;    //q1=undefined
</script>
```

## 闭包的应用

- 模块化
- 防止变量被破坏

举例：模块化的应用参考Vue.js中的源码

防止变量被破坏，运用场景

举例：KTV中的最低消费，最低消费的数值不能够暴露出去让别人随便修改，这样会脏了数据，返回一个对象暴露方法来进行业务操作，而不是进行直接操作。

``` html
<!DOCTYPE html>
<html>
<head>
    <title></title>
</head>
<body>
    <script type="text/javascript">
        //场景举例
        function KTV(){

            //最低消费1000
            var leastPrice = 1000;

            var sale = 0;

            return {
                //消费
                pay:function(pr){
                    sale += pr;
                },
                //结账
                settlement:function(){
                    if(sale<leastPrice){
                        console.log("您未达到最低消费，请继续消费");
                    } else {
                        console.log("欢迎下次光临");
                    }
                }

            }

        }
        var p1 = KTV();
        p1.pay(100);
        p1.settlement(); //您未达到最低消费，请继续消费
        p1.pay(1000);
        p1.settlement(); //欢迎下次光临

    </script>
</body>
</html>
```

但是如果需要进行修改最低消费，场景：老板的朋友进行消费，需要修改

``` html
<!DOCTYPE html>
<html>
<head>
    <title></title>
</head>
<body>
    <script type="text/javascript">
        //场景举例
        function KTV(){

            //最低消费1000
            var leastPrice = 1000;

            var sale = 0;

            return {
                //消费
                pay:function(pr){
                    sale += pr;
                },
                //结账
                settlement:function(){
                    if(sale<leastPrice){
                        console.log("您未达到最低消费，请继续消费");
                    } else {
                        console.log("欢迎下次光临");
                    }
                },
                //传入工号，和修改最低消费值
                edit:function(id,lp){
                    if(id == 888){
                        leastPrice = lp;
                    }else{
                        console.log("你不是管理员，无法修改");
                    }
                }

            }

        }
        var p1 = KTV();
        p1.pay(100);
        p1.settlement(); //您未达到最低消费，请继续消费
        p1.edit(100, 200); //你不是管理员，无法修改
        p1.edit(888, 100); //欢迎下次光临
        p1.settlement();
    </script>
</body>
</html>
```

闭包的运用防止变量被破坏，暴露出方法来间接修改数值，防止出现脏数据。
闭包中也有作用域链的相关运用。

# 严格模式

从 ES5 开始，函数内部可以设定为严格模式。

严格模式主要有以下限制。

- 变量必须声明后再使用
- 函数的参数不能有同名属性，否则报错
- 不能使用`with`语句
- 不能对只读属性赋值，否则报错
- 不能使用前缀 0 表示八进制数，否则报错
- 不能删除不可删除的属性，否则报错
- 不能删除变量`delete prop`，会报错，只能删除属性`delete global[prop]`
- `eval`不会在它的外层作用域引入变量
- `eval`和`arguments`不能被重新赋值
- `arguments`不会自动反映函数参数的变化
- 不能使用`arguments.callee`
- 不能使用`arguments.caller`
- 禁止`this`指向全局对象
- 不能使用`fn.caller`和`fn.arguments`获取函数调用的堆栈
- 增加了保留字（比如`protected`、`static`和`interface`）

上面这些限制，模块都必须遵守。由于严格模式是 ES5 引入的，不属于 ES6，所以请参阅相关 ES5 书籍，本书不再详细介绍了。

其中，尤其需要注意`this`的限制。ES6 模块之中，顶层的`this`指向`undefined`，即不应该在顶层代码使用`this`。

# 函数的四种调用方式

为什么要区分函数的调用方式呢？是因为在ES6前，函数内部的this是由该函数的调用方式决定的，函数内部的this跟大小写、书写位置无关。

## 函数调用方式

``` javascript
function Person(){
    console.log(this);
}

Person(); //Window
```

可以看到打印的this指向的是window对象。

在看第二种情况

``` javascript
var p = {
         say:function(){
         console.log(this);
    }
}

var s = p.say;
s(); //Window

Person.prototype.say = function(){
     console.log(this);
}

var f1 = Person.prototype.say;
f1(); //Window
```

this的指向还是window对象。

而在严格模式下

``` javascript
function Person(){
    'use strict'
    console.log(this);
}

Person(); //undefined
```

``` javascript
var p = {
    say: function () {
        'use strict'
        console.log(this);
    }
}

var s = p.say;
s(); //undefined

Person.prototype.say = function () {
    'use strict'
    console.log(this);
}

var f1 = Person.prototype.say;
f1(); //undefined
```

>可以看到，如果是以函数方式来调用，函数内部的this将指向window对象。而在严格模式下，由于函数调用方式this都是指向顶层对象，而严格模式禁止this指向顶层对象，所以this都是undefined

## 方法调用方式

``` javascript
function Person(){
        //这里不讨论age和say方法中的this
         this.age = 10;
         this.say = function(){
         console.log(this);
    }
}

var p1 = new Person();
p1.say(); //Person
```

this指向了p1对象。

``` javascript
function Person(){
    this.age = 10;
}

Person.prototype.say = function(){
    console.log(this);
}

var p2 = new Person();
p2.say(); //Person
```

this指向了p2对象。

``` javascript
var p3 = {
    age: 10,
    say: function(){
        console.log(this);
    }
}
p3.say(); //p2
```

this指向了p3对象。

``` javascript
var clear = function(){
    console.log(this.length);
}

var tom = {
    c: clear,
    length: 20
}

tom.c(); //20
```

可以看到c函数还是使用方法调用的方式来调用的，c指向了clear函数，所以函数体中的this指向了tom，所以打印了20.

>可以看到，使用方法方法调用的this会指向调用该方法的对象。

## 构造函数调用方式

``` javascript
function Person(){
    this.age = 20;
}
var p1 = new Person(); //p1
```

通过new关键字来调用的，那么这种方式就是构造函数的调用方式，这种方式创建的函数内部this将指向该构造函数的实例对象。

``` javascript
function jQuery(){
        var _init=jQuery.prototype.init;
        //如果函数返回了一个复杂数据类型的值，那么本次函数的返回值就是该值。
        return new _init();
    }
    jQuery.prototype={
        constructor:jQuery,
        length:100,
        init:function(){
            //this指向init构造函数的实例
            //-->1、首先查看本身有没有length属性
            //-->2、如果本身没有该属性，那么去它的原型对象中查找
            //-->3、如果原型对象中没有，那么就去原型对象的原型对象中查找，最终一直找到根对象（Object.prototype）
            //-->4、最终都没有找到的话，我们认为该对象并没有该属性，如果获取该属性的值：undefined
            console.log(this.length);   
        }
    }

new jQuery(); //undefined
```

上述说了this指向该构造函数的实例对象，可以访问自身实例的属性和方法，这里返回了`init.prototype`的属性，但是这并不是`Jquery.prototype`的属性，所以打印的是`undefined`。

``` javascript
//修改了init函数的默认原型，指向新原型
jQuery.prototype.init.prototype = jQuery.prototype;
```

如果我们修改了`init.prototype`指向了`jQuery.prototype`，那么再运行上述代码，`this.length`打印的值将是100。

## 上下文调用方式

### call

``` javascript
function fcall(){
    console.log(this);
}

fcall.call(this); //Window
fcall.call([1,3,5]); //[1, 3, 5]
fcall.call(1); //Number
fcall.call("abc"); //String
fcall.call(true); //Boolean
fcall.call(null); //Window
fcall.call(new fcall()); //fcall
fcall.call({age:20,height:1000}); //{age: 20, height: 1000}
fcall.call(undefined); //Window
```

call方法的第一个参数就决定了函数内部this的指向。
如果第一个参数的类型是：
- 对象类型：那么this将指向该对象
- 如果是`undefined`,`null`，那么this将指向Window
- 如果是普通的数据类型比如`"abc"`,`1`,`true`则会转换成对应构造函数的实例（装箱），例如`String`,`Number`,`Boolean`。

### apply

apply和call方法调用方式可以完全相同，只是后面传参数时有异同。

``` javascript
function toString(a,b,c){
    console.log(a+" "+b+" "+c);
}
toString.call(null,1,3,5)   //"1 3 5"
toString.apply(null,[1,3,5])//"1 3 5"
```

### bind

bind是es5中才有的(IE9+)

``` javascript
var obj = {
    age: 10,
    run: function(){
        setTimeout(function(){
            console.log(this.age); //undefined
        }, 50);
    }
}
obj.run();
```

以前这样书写时，可能会定义一个另外的变量来指向this

``` javascript
var obj = {
    age: 10,
    run: function(){
        var _that = this;
        setTimeout(function(){
            console.log(_that.age); //10
        }, 50);
    }
}
obj.run();
```

但是现在可以使用bind来实现了。

``` javascript
var obj = {
    age: 10,
    run: function(){
        setTimeout((function(){
            console.log(this.age); //10
        }).bind(this), 50);
    }
}
obj.run();
```

可以发现，使用bind方式后this的指向将改为了obj。

更直观的了解bind方法。

``` javascript
function speed(){
    console.log(this.speed);
}

var speedOther = speed.bind({ speed: 20 });
speedOther(); // 20
```

speed.bind方法执行后会产生了一个新的函数，新的函数体内与原来的是一样的，但是唯一的不同就是this的指向改为了`{ speed: 20 }`。

``` javascript
var obj={
    name:"西瓜",
    drink:(function(){
        //this指向了：{ name:"橙汁" }
        console.log(this.name);
    }).bind({ name:"橙汁" })
}
obj.drink();    //"橙汁"
```

### 三者的区别

call\apply是立刻执行了这个函数，并且在执行过程中绑定了this的值，而bind并没有立刻执行这个函数，而是产生了一个新的函数，新的函数绑定了this的值。

## ES6中的箭头函数

```js
//匿名函数
div.onclick = function () {
    console.log("你好")
}
//箭头函数
div.onclick = () => {
    console.log("你好")
}
```
+ 有一个参数的箭头函数
```js
var fn = (a) => {
    console.log("abc");
}
//等价于：
var fn = a => {
    console.log("abc");
}
```
+ 有2个及更多参数的箭头函数
```js
var f=(a,b,c)=>{
    console.log("abc")
}
```

**箭头函数和普通匿名函数有哪些不同？**
- 函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象。
- 不可以当作构造函数，也就是说，不可以使用new命令，否则会抛出一个错误。
- 不可以使用arguments对象，该对象在函数体内不存在。如果要用，可以用 rest 参数代替。
- （不常用）不可以使用yield命令，因此箭头函数不能用作 Generator 函数。 
- generator函数现在经常用async替代

# 数据类型判断

+ typeof 
    - typeof只能判断：数字、字符串、布尔值、undefined、函数
+ `Object.prototype.toString.call()`
    - 5  `[object Number]`
    - "abc"`[object String]`
    - true `[object Boolean]`
    - null `[object Null]`
    - undefined `[object Undefined]`
    - [1,3,5] `[object Array]`
    - function(){} `[object Function]`
    - new Date()   `[object Date]`
    - /abc/       `[object RegExp]`
+ Array.isArray()  es5中提出来的检测数组
+ isNaN()   
+ isInfinity()