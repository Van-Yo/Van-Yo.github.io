---
title: Js中关于this、apply、call、bind的深入探讨
author: Revan
index_img: /img/js-this.jpg
category: 技术
tags:
  - Javascript
  - 面试
excerpt: 在看框架源码或者面试题中，经常会看到this、apply、call、bind这些字眼，这些对于大多数前端学习者来说都是不陌生的，然后真正理解它们或者全部刷对它们的面试题还是有些困难，因此本文旨对这一系列的知识进行深入研究。
date: 2021-03-09 16:49:29
---
## 引言
{% note info %}
这又是一个面试经典问题~/(ㄒoㄒ)/~~也是 `ES5` 中众多坑中的一个，在 `ES6` 中可能会极大避免 `this` 产生的错误，但是为了一些老代码的维护，最好还是了解一下 `this` 的指向和 `call`、`apply`、`bind` 三者的区别。
{% endnote %}

## this 的指向
在 `ES5` 中，其实 `this` 的指向，始终坚持一个原理：**this 永远指向最后调用它所在函数的那个对象**。

举个栗子：
```js
var a = {
    name: "Cherry",
    fn : function () {
        console.log(this.name);      // Cherry
    }
}
a.fn();
```
用上面这个栗子来解释原理：`this`在`fn`函数中，`fn`函数被调用，而调用它的对象是`a`，所以`this`指向对象`a`。

接下来利用几种 `JS` 中的函数调用来具体说明 `this` 的指向：

## JS 中的函数调用
函数调用的方法一共有 `4` 种
1. 作为一个函数调用
2. 函数作为方法调用
3. 使用构造函数调用函数
4. 作为函数方法调用函数（call、apply）


### 作为一个函数调用
例 1：
```js
var name = "windowsName";
function a() {
    var name = "Cherry";

    console.log(this.name);          // windowsName

    console.log("inner:" + this);    // inner: Window
}
a();
console.log("outer:" + this)         // outer: Window
```
这样一个最简单的函数，不属于任何一个对象，就是一个函数，这样的情况在 `JavaScript` 的在浏览器中的`非严格模式`默认是属于全局对象 `window` 的，在严格模式，就是 `undefined`。

但这是一个全局的函数，很容易产生命名冲突，所以不建议这样使用。
### 函数作为方法调用
更多的情况是将函数作为对象的方法使用。比如例 2：
```js
var name = "windowsName";
var a = {
    name: "Cherry",
    fn : function () {
        console.log(this.name);      // Cherry
    }
}
a.fn();
```
这里定义一个对象 `a`，对象 `a` 有一个属性（`name`）和一个方法（`fn`）。

然后对象 `a` 通过 `.` 方法调用了其中的 `fn` 方法。

然后我们一直记住的那句话“**this 永远指向最后调用它的那个对象**”，所以在 `fn` 中的 `this` 就是指向 `a` 的。
### 使用构造函数调用函数
### 作为函数方法调用函数