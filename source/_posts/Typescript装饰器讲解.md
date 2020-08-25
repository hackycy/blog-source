---
title: Typescript装饰器讲解
date: 2020-08-25 12:22:26
keywords:
    - TypeScript
    - Decorators
tags:
    - TypeScript
categories:
    - TypeScript
---

# 介绍

装饰器（Decorators）为我们在类的声明及成员上通过元编程语法添加标注提供了一种方式。 Javascript里的装饰器目前处在 [建议征集的第二阶段](https://github.com/tc39/proposal-decorators)，但在TypeScript里已做为一项实验性特性予以支持。

> 注意  装饰器是一项实验性特性，在未来的版本中可能会发生改变。

<!-- more -->

若要启用实验性的装饰器特性，你必须在命令行或`tsconfig.json`里启用`experimentalDecorators`编译器选项：

**命令行**:

```shell
tsc --target ES5 --experimentalDecorators
```

**tsconfig.json**:

```json
{
    "compilerOptions": {
        "target": "ES5",
    		"experimentalDecorators": true,
    		"emitDecoratorMetadata": true
    }
}
```

# 使用

装饰器是一种特殊类型的声明，它能够被附加到类声明，方法， 访问符，属性或参数上，可以修改类的行为。 装饰器使用 `@expression`这种形式，`expression`求值后必须为一个函数，它会在运行时被调用，被装饰的声明信息做为参数传入。

例：

```typescript
@Path('/hello')
class HelloService {}
```

## 如何定义装饰器

装饰器本身其实就是一个函数，理论上忽略参数的话，任何函数都可以当做装饰器使用。例：

demo.ts

```typescript
function Path(target:any) {
    console.log("I am decorator.")
}

@Path
class HelloService {}
```

使用`tsc`编译后,执行命令`node demo.js`，输出结果如下：

```bash
I am decorator.
```

## 装饰器执行时机

修饰器对类的行为的改变，是代码编译时发生的（不是TypeScript编译，而是js在执行机中编译阶段），而不是在运行时。这意味着，修饰器能在编译阶段运行代码。也就是说，修饰器本质就是编译时执行的函数。
在Node.js环境中模块一加载时就会执行

## 函数柯里化解决参数问题

但是实际场景中，有时希望向装饰器传入一些参数, 如下：

```typescript
@Path("/hello", "world")
class HelloService {}
```

此时上面装饰器方法就不满足了（VSCode编译报错），这是我们可以借助JavaScript中函数柯里化特性

```typescript
function Path(p1: string, p2: string) {
    return function (target) { //  这才是真正装饰器
        // do something 
    }
}
```

## 五种装饰器

在TypeScript中装饰器可以修饰四种语句：类，属性，访问器，方法以及方法参数。

### 1 类装饰器

应用于类构造函数，其参数是类的构造函数。
注意`class`并不是像Java那种强类型语言中的类，而是JavaScript构造函数的语法糖。

```typescript
function Path(path: string) {
    return function (target: Function) {
        !target.prototype.$Meta && (target.prototype.$Meta = {})
        target.prototype.$Meta.baseUrl = path;
    };
}

@Path('/hello')
class HelloService {
    constructor() {}
}

console.log(HelloService.prototype.$Meta);// 输出：{ baseUrl: '/hello' }
let hello = new HelloService();
console.log(hello.$Meta) // 输出：{ baseUrl: '/hello' }
```

### 2 方法装饰器

它会被应用到方法的 属性描述符上，可以用来监视，修改或者替换方法定义。
方法装饰会在运行时传入下列3个参数：

- 1、对于静态成员来说是类的构造函数，对于实例成员是类的原型对象。
- 2、成员的名字。
- 3、成员的属性描述符。

```typescript
function GET(url: string) {
    return function (target, methodName: string, descriptor: PropertyDescriptor) {
        !target.$Meta && (target.$Meta = {});
        target.$Meta[methodName] = url;
    }
}

class HelloService {
    constructor() { }
    @GET("xx")
    getUser() { }
}

console.log((<any>HelloService).$Meta);
```

注意：在vscode编辑时有时会报`作为表达式调用时，无法解析方法修饰器的签名。`错误，此时需要在tsconfig.json中增加target配置项：

```typescript
{
    "compilerOptions": {
        "target": "es6",
        "experimentalDecorators": true,
    }
}
```

### 3 方法参数装饰器

参数装饰器表达式会在运行时当作函数被调用，传入下列3个参数：

- 1、对于静态成员来说是类的构造函数，对于实例成员是类的原型对象。
- 2、参数的名字。
- 3、参数在函数参数列表中的索引。

```typescript
function PathParam(paramName: string) {
    return function (target, methodName: string, paramIndex: number) {
        !target.$Meta && (target.$Meta = {});
        target.$Meta[paramIndex] = paramName;
    }
}

class HelloService {
    constructor() { }
    getUser( @PathParam("userId") userId: string) { }
}

console.log((<any>HelloService).prototype.$Meta); // {'0':'userId'}
```

### 4 属性装饰器

属性装饰器表达式会在运行时当作函数被调用，传入下列2个参数：

- 1、对于静态成员来说是类的构造函数，对于实例成员是类的原型对象。
- 2、成员的名字。

```typescript
function DefaultValue(value: string) {
    return function (target: any, propertyName: string) {
        target[propertyName] = value;
    }
}

class Hello {
    @DefaultValue("world") greeting: string;
}

console.log(new Hello().greeting);// 输出: world
```

### 装饰器加载顺序

```typescript
function ClassDecorator() {
    return function (target) {
        console.log("I am class decorator");
    }
}
function MethodDecorator() {
    return function (target, methodName: string, descriptor: PropertyDescriptor) {
        console.log("I am method decorator");
    }
}
function Param1Decorator() {
    return function (target, methodName: string, paramIndex: number) {
        console.log("I am parameter1 decorator");
    }
}
function Param2Decorator() {
    return function (target, methodName: string, paramIndex: number) {
        console.log("I am parameter2 decorator");
    }
}
function PropertyDecorator() {
    return function (target, propertyName: string) {
        console.log("I am property decorator");
    }
}

@ClassDecorator()
class Hello {
    @PropertyDecorator()
    greeting: string;


    @MethodDecorator()
    greet( @Param1Decorator() p1: string, @Param2Decorator() p2: string) { }
}
```

输出结果：

```bash
I am property decorator
I am parameter2 decorator
I am parameter1 decorator
I am method decorator
I am class decorator
```

从上述例子得出如下结论：

1、有多个参数装饰器时：从最后一个参数依次向前执行

2、方法和方法参数中参数装饰器先执行。

3、类装饰器总是最后执行。

4、方法和属性装饰器，谁在前面谁先执行。因为参数属于方法一部分，所以参数会一直紧紧挨着方法执行。上述例子中属性和方法调换位置，输出如下结果：

```
I am parameter2 decorator
I am parameter1 decorator
I am method decorator
I am property decorator
I am class decorator
```

# 参考资料

- 摘抄自 - https://www.cnblogs.com/winfred/p/8216650.html

- [Github Sample](https://github.com/hackycy/practice-examples/tree/master/typescript/DecoratorDemo)

- https://www.tslang.cn/docs/handbook/decorators.html