---
title: 一次仿写jQuery库的简单代码
date: 2019-04-25 22:39:54
tags:
    - JavaScript
categories:
    - JavaScript
---

**功能的实现都很简单，但是是需要学着别人的思想**

<!-- more -->

``` html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script>
        //模块化：内部的东西对外是封闭的，只暴露出$,jQuery操作。
        (function (global) {

            function jQuery(selector) {

                // //获取页面中所有指定的元素
                // const elements = document.querySelectorAll(selector);
                // console.log(elements);

                // //但是随着$()操作的增加，将会产生无数个css方法，浪费了很多的内存
                // elements.css = function(){
                //     console.log('query css');
                // }
                // return elements;
                var _init = jQuery.prototype.init;

                // console.log(jQuery.prototype);

                return new jQuery.prototype.init(selector);
            }

            // 为了方便少写prototype单词，简化
            jQuery.fn = jQuery.prototype = {

                constructor: jQuery,

                init: function (selector) {
                    // // 把查找到的DO吗元素放在变量中
                    // const elements = document.querySelectorAll(selector);
                    // // this.elements = elements;
                    // // 但是jQuery为了操作方便，所以将DOM元素放在了自己身上，也就是伪对象数组
                    // for (let i = 0; i < elements.length; i++) {
                    //     this[i] = elements[i];
                    // }
                    // this.length = elements.length;

                    //判断如果是一个string，则查找
                    if (jQuery.type(selector) === 'string') {

                        const elements = document.querySelectorAll(selector);
                        // this.elements = elements;
                        // 但是jQuery为了操作方便，所以将DOM元素放在了自己身上，也就是伪对象数组
                        for (let i = 0; i < elements.length; i++) {
                            this[i] = elements[i];
                        }
                        this.length = elements.length;
                    } else { //如果是一个对象，则直接赋值
                        this[0] = selector;
                        this.length = 1;
                    }
                },


                // css(attr, value) {
                //     for (let i = 0; i < this.length; i++) {
                //         this[i].style[attr] = value;
                //     }
                // }

            }

            jQuery.fn.init.prototype = jQuery.fn;

            // jQuery插件编写之路
            // 无法使用箭头函数
            jQuery.fn.extend = jQuery.extend = function (...args) {

                if (args.length <= 0) {
                    return;
                }

                let target, source = null;

                source = [...args];

                // $.extend | $.fn.extend
                if (args.length == 1) {
                    target = this;
                } else {
                    // args[0].extend
                    target = args[0];
                    source.splice(0, 1);
                }

                // 判断当前是谁
                // if (this === jQuery) {
                //     target = args[0];
                //     // 删除第一个元素
                //     source.splice(0, 1);
                // } else {
                //     target = this;
                // }

                // console.log(target);
                // console.log(source);

                // source.forEach((item,index) => {
                //     for (const key in item) {
                //         target[key] = item[key];
                //     }
                // });

                //for循环优化
                // Object.assign() 方法用于将所有可枚举属性的值从一个或多个源对象复制到目标对象。它将返回目标对象。 有兼容性问题
                Object.assign(target, ...source);

                return target;

            }

            //添加一些工具类方法：$.xxx()
            //$.ajax  $.post $.get  $.type $.each
            jQuery.extend({
                //$.each([1,3,5],function(index,value){})
                //$.each({ age:18,height:200 },function(key,value){})
                //可以遍历数组和对象
                each(target, callback) {

                    //对象有两种，数组对象和普通数组

                    //不仅仅可以遍历数组，也可以遍历伪数组
                    //{ length:0 }
                    //{ 0:100,length:1 }
                    //{ 0:"a",1:"b",2:"c",length:3 }

                    if ((length in target) && target.length >= 0) {

                        for (let i = 0; i < target.length; i++) {

                            callback.call(target[i], i, target[i]);

                        }

                    } else {
                        // 普通对象

                        // //获取到p1自身拥有的属性组成数组
                        // Object.keys(target).forEach((value,index)=>{
                        //     console.log(value,index);
                        // })

                        for (const key in target) {
                            if (target.hasOwnProperty(key)) {
                                callback.call(target[key], key, target[key]);
                            }
                        }

                    }

                },
                type(target) {
                    // console.log('type');
                    var type = Object.prototype.toString.call(target);
                    return type.replace("[object ", "")
                        .replace("]", "")
                        .toLowerCase();
                },
                ajax() {
                    console.log('ajax');
                }
            });

            const events = [
                //{ ele:div1,type:"click",callback:function(){} },
                //{ ele:div1,type:"click",callback:function(){} }
            ];

            jQuery.fn.extend({
                // 遍历自己
                each(callback) {
                    jQuery.each(this, callback);
                },
                //1、获取样式$("div").css("color")  只能获取到第一个div的颜色
                //2、设置样式
                //      $("div").css("color","red") 设置每一个div的字体颜色
                //      $("div").css({ color:"red","backgroundColor","blue" })
                css(...args) {
                    if (args.length === 1) {
                        // 一个参数时分两种情况，如果是一个字符串，则为获取属性，但是如果为一个对象则为设置样式
                        if (jQuery.type(args[0]) === 'string') {
                            // console.log(this); this指向$("div"),init方法放回的元素
                            //不管元素有多少个，只取第一个
                            let $this = this[0];
                            let style = window.getComputedStyle($this, null);
                            // console.log(style);
                            return style[args[0]];
                        } else {
                            // 设置样式,需要遍历选择器
                            // jQuery.each(this, function(){
                            //     let $this = this;
                            //     jQuery.each(args[0], function(key, value){
                            //         $this.style[key] = value;
                            //     })
                            // });

                            //优化上述代码
                            let _that = this;
                            jQuery.each(args[0], function (key, value) {

                                _that.css(key, value);

                            });

                            return this; //链式调用
                        }
                    } else {
                        // 参数为两个，则为一个属性，一个属性值
                        // 设置单个样式
                        // let key = args[0];
                        // let value = args[1];
                        this.each(function () {
                            this.style[args[0]] = args[1];
                        });
                        return this; //链式调用
                    }
                },
                show() {
                    this.css('display', 'block');
                },
                hide() {
                    this.css('display', 'none');
                },
                toggle() {
                    let _that = this; //this -> jQuery
                    this.each(function () {
                        // this -> dom 
                        // if (jQuery(this).css('display') === 'block') {
                        //     jQuery(this).css('display', 'none');
                        // }else {
                        //     jQuery(this).css('display', 'block');
                        // }

                        //优化上述代码

                        let $this = jQuery(this);
                        $this.css('display') === 'block' ? $this.hide() : $this.show();
                    })
                },
                // 添加event
                on(type, callback) {

                    this.each(function () {

                        // console.log(this);

                        this.addEventListener(type, callback);

                        events.push({
                            ele: this,
                            type: type,
                            callback: callback
                        });

                    });

                    return this; //链式编程

                },
                //解绑绑定：$("div").off("click")：表示解除当前元素的所有的单击事件
                off(type) {

                    this.each(function () {

                        let _that = this;

                        let es = events.filter(function (e, i) {

                            console.log(i, e);

                            //查看绑定的元素是否存在events数组中，并且有没有该元素绑定的事件,并且移除
                            let isCurent =  e.ele === _that && e.type === type
                            if (isCurent) {
                                events.splice(i, 1);
                            }
                            return isCurent;
                        });

                        if (es.length <= 0) {
                            return;
                        }

                        //可能存在多次绑定
                        es.forEach(function (e) {

                            console.log(e, _that);

                            let { callback } = e;

                            _that.removeEventListener(type, callback);
                        });


                        console.log(events);

                    })

                }
            });

            // function F(selector) {
            //     // 把查找到的DO吗元素放在变量中
            //     const elements = document.querySelectorAll(selector);
            //     // this.elements = elements;
            //     // 但是jQuery为了操作方便，所以将DOM元素放在了自己身上，也就是伪对象数组
            //     for (let i = 0; i < elements.length; i++) {
            //         this[i] = elements[i];
            //     }
            //     this.length = elements.length;
            // }

            // F.prototype.css = function(attr, value) {
            //     console.log(this.elements.length);
            //     for(let i=0; i<this.elements.length; i++) {
            //         this.elements[i].style[attr] = value;
            //     }
            // }

            // F.prototype = {
            //     constructor: F,
            //     css(attr, value) {
            //         for (let i = 0; i < this.length; i++) {
            //             this[i].style[attr] = value;
            //         }
            //     }
            // }

            // 相当于赋值给顶层对象window的属性
            global.$ = global.jQuery = jQuery;

        })(window); //自调用函数

        // console.log($);

        window.onload = function () {
            // $('div').css('color', 'red');
            // $('.header').css('color', 'blue');

            // var p = {};
            // $.extend(p, { a: 1 }, { b: 2 });
            // console.log(p);

            // $.fn.extend({ a: 0 });
            // console.log($.fn);

            // $.each({ a: 'a', b: 'b' }, function (key, value) {
            //     console.log(key, value);
            // });
            // $.each({ 0: 100, 1: 200, 2: 300, length: 3 }, function (key, value) {
            //     console.log(key, value);
            // });
            // $.each([1, 2, 3], function (key, value) {
            //     console.log(key, value);
            // });

            // console.log($.type({ a: 1 }));

            // $('div').css({
            //     'color': 'red',
            //     'backgroundColor': 'blue'
            // })

            // $('div').css('color', 'red');

            // $('.header').hide();

            // $('div').toggle();

            $('.footer').on('click', function (e) {
                alert('footer');
            });

            $('.header').on('click', function (e) {
                alert('header');
            });

            $('.section').on('click', function (e) {
                $('.footer').off('click');
                console.log('remove')
            });

            // $('.header').off('click');
        }
    </script>
    <style>
        div:first-child {
            color: rebeccapurple;
            /* display: none; */
        }
    </style>
</head>

<body>
    <div class="footer">aaa</div>

    <br>
    <br>    
    <div class="header">bbb</div>
    <div class="section">ccc</div>
</body>

</html>
```
