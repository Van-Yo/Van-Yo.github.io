---
title: JS运行机制
author: Revan
date: 2021-01-19 15:12:55
index_img: /img/event-loop.jpg
category: 技术
tags:
  - Javascript
  - Node
excerpt: 学习JS一定要理清它的运行机制，这样在平时写项目或者调试bug的时候，会很容易知道该怎么写或者bug出在哪里。对于这一块的学习，也能方便在面试中问答自如，本文旨系统性地理清JS运行机制。
---

{% note info %}
`Event Loop` 是一个很重要的概念，指的是计算机系统的一种运行机制。那么 `JS` 的 `运行机制` 是怎样的呢？
{% endnote %}

## 学习目录
{% cb JS同步模式与异步模式, true %}
{% cb EventLoop, true %}
{% cb 宏任务（Tasks）与微任务（Microtasks）, true %}

## 同步模式与异步模式
首先要确定 `js` 是 `单线程语言` ，`js`在设计之初用作用户互动，以及操作`DOM`。这决定了它只能是`单线程`（例如`多线程`操作同一`dom`，一个删除一个修改，这样会产生冲突）。[^1]

但倘若只有`同步模式`，遇到耗时操作，页面便会`阻塞`，就像接口请求不到数据，或者图片未加载完成，页面就卡住一直等待。这样显然不现实也不实用。

所以`异步模式`应运而生。你可能会有疑问，`单线程`的`js`是怎么完成`异步操作`的，可以这么理解:`js`是`单线程`语言，但`运行环境`可以`开多线程`帮助处理（例如: `浏览器`，`node`..）。

`js`后推出的`Worker类`，也是这么实现的。

来个栗子：
```js
console.log('global begin');
setTimeout(function timer1(){
    console.log('timer1 invoke')
},1800);
setTimeout(function timer2(){
    console.log('timer2 invoke');
    setTimeout(function inner(){
        console.log('inner invoke');
    },1000);
},1000);
console.log('global end');

//result:
// global begin
// global end
// timer2 invoke
// timer1 invoke
// inner invoke
```
分析一下js代码运行的顺序:
1. 首先运行`主线程`。`console.log同步代码`直接压入执行栈，执行并弹出，页面打印 `global begin`；
2. 遇到`setTImeout异步代码`，函数进入`event Table`（异步事件注册表）并注册函数，`webAPIs`（浏览器）帮助我们倒计时，倒计时结束，再将回调函数放入`event Queue`（事件队列，消息队列），等待主线程运行完毕，会自动将`event Queue`（事件队列，消息队列）里的任务放入`主线程`继续执行；
3. 所以`timer1`、`timer2`进入`event Table`（异步事件注册表）并注册函数，`webAPIs`（浏览器）帮助我们`一起倒计`时。主线程继续执行，打印`global end`，到此为止，`主线程`上的任务全部结束，主要是`同步任务`。
4. 因为`timer2`的倒计时短，所以1000ms后放入`event Queue`队列，因为主线程任务已经完成了，所以`timer2`开始执行，打印`timer2 invoke`。又碰到`inner`，是个`setTImeout异步代码`，注册`inner`开始倒计时。因为`timer1`已经计时完1000ms还剩800ms，而`inner`还剩1000ms,所以再过800ms`timer1`会先放入`event Queue`队列，因为主线程上没有任务了，所以`timer1`也会放入`主线程`继续执行，所以完后打印`timer1 invoke`。
5. 最后200ms后再去队列取事件，打印`inner invoke`。

{% note warning %}
这里有一个大坑，例如`setTimeout`设置`3000`，延时3秒操作，但通常`不是严格`3s后便会执行，4s？5s？ 之所以这样是因为，回调函数3s后放入队列，`等待主线程完成才会执行`。主线程的执行时间那就不知道了，假如主线程的执行时间是4s，4s>3s，所以4s后才能执行回调函数。
{% endnote %}

## EventLoop
所谓事件环，就是三步不断循环的js的执行闭环，上面的粒子已经描述的很详细了。
1. 同步主线程
2. 异步函数放入eventTable注册，等待完成后放入eventQueue
3. 同步主线程完成，取eventQueue放入主线程

## 宏任务（Tasks） 与微任务（Microtasks）
> 两个任务皆为 异步任务，区别就是执行顺序。 我总结一句话，消息队列 有微先走微，微可插宏队。

- 宏任务：script（主线程）、setTimeout、setInterval、setImmediate
- 微任务：Promise的then（promise传入的执行函数会立即执行属于同步）、process.nextTick（node环境）、Object.observe(已废弃)、 MutationObserver（观测dom变化）

栗子：
```js
Promise.resolve().then(()=>{
  console.log('Promise1')  
  setTimeout(()=>{
    console.log('setTimeout2')
  },0)
})

setTimeout(()=>{
  console.log('setTimeout1')
  Promise.resolve().then(()=>{
    console.log('Promise2')    
  })
},0)

// Promise1，setTimeout1，Promise2，setTimeout2
```
栗子解析：
1. 先走主线程，`promise`直接`resolve`，`then`里面函数属于`异步微任务`，下面的`setTimeout1`属于`异步宏任务`，都放入事件环；
2. 当主线程走完，将事件环的函数放入主线程，`先微后宏`，打印`Promise1`，然后再次遇到`setTimeout`，放入事件环。
3. `setTimeout1`回调执行， 打印`setTimeout1`，遇到`promise`放入事件环，`主线程第二遍`走完，现在事件环有 `setTimeout2`和`Promise2`。
4. 微任务可插队宏任务，先打印 `Promise2`，再打印 `setTimeout2`

## 总结
在了解完JS运行机制之后，只要捎带刷一刷类似的栗子就ok了。

## 参考
[^1]: [一文搞懂js运行机制，宏任务微任务event loop](https://juejin.cn/post/6913835186933366798)