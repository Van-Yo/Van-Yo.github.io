---
uuid: 9685f54c-b6b3-8664-58cc-b95ec17e77b2
title: 浅谈ES6 Promise 和 ES7 Async/await的使用
date: 2020-10-22 21:06:45
categories: 技术
tags: 
- Javascript
- ES6
- ES7
---
众所周知，Javascript是单线程的，所谓"单线程"，就是指一次只能完成一件任务。如果有多个任务，就必须排队，前面一个任务完成，再执行后面一个任务，以此类推。这种模式的好处是实现起来比较简单，执行环境相对单纯；坏处是只要有一个任务耗时很长，后面的任务都必须排队等着，会拖延整个程序的执行。常见的浏览器无响应（假死），往往就是因为某一段Javascript代码长时间运行（比如死循环），导致整个页面卡在这个地方，其他任务无法执行。为了解决这个问题，Javascript语言将任务的执行模式分成两种：同步（Synchronous）和异步（Asynchronous）。当我们处理异步时，有三种常用的方法可以选择：回调，Promise和Async/await，接下来简述一下三者的使用区别和优缺点。
## 前言
众所周知，Javascript是`单线程`的，所谓"单线程"，就是指一次只能完成一件任务。如果有`多个任务`，就`必须排队`，前面一个任务完成，再执行后面一个任务，以此类推。这种模式的好处是实现起来比较简单，执行环境相对单纯；坏处是只要有一个任务耗时很长，后面的任务都必须排队等着，会`拖延整个程序的执行`。常见的浏览器无响应（假死），往往就是因为某一段Javascript代码长时间运行（比如死循环），导致整个页面卡在这个地方，其他任务无法执行。为了解决这个问题，Javascript语言将任务的`执行模式`分成两种：`同步`（Synchronous）和`异步`（Asynchronous）。当我们处理异步时，有三种常用的方法可以选择：异步回调，Promise和Async/await，接下来简述一下三者的使用区别和优缺点。
## 异步回调
回调是一个函数被作为一个`参数`传递到另一个函数里，在那个函数执行完后再执行，也就是B函数被作为参数传递到A函数里，在A函数执行完后再执行B，用代码表示就是如下：
```js
function A(callback){
　　callback();
}
function B(){}
A(B)
```
> 这也未免太简单了，是的，但现实往往就是简单的东西，项目中不会用到，太可怜了。而难的东西却一定是在简单东西基础上构建的。

那回调和异步有关系吗？回调一定是`异步`的吗，看上面`A(B)`的例子就知道，明明还是`同步`，所以`回调并不一定就是异步，他俩并没有直接的关系`。
下面举一个真实的例子：
有三个函数，分别是task1，task2和task3，这三个都是同步任务，并且task2必须等到task1完成后才能执行，task3执行顺序不做要求，那么常规我们会写如下代码：
```js
function task1(){
    console.log('task1');
}
function task2(){
    console.log('task2');
}
function task3(){
    console.log('task3');
}
function testAsync(){
    task1();
    task2();
    task3();
}

testAsync();
// task1
// task2
// task3
```
这样写并没有做`回调`，但效果已经达到了，task2确实在task1之后执行的。那接下来使用回调：
```js
function task1(task){
    console.log('task1');
    task();
}
function task2(){
    console.log('task2');
}
function task3(){
    console.log('task3');
}
function testAsync(){
    task1(task2);
    task3();
}

testAsync();
// task1
// task2
// task3
```
输出结果一样，没有影响到task3的执行顺序，这就表明，该回调是`同步回调`。那什么是`异步回调`呢？

因为js是单线程的，但是有很多情况的执行步骤（ajax请求远程数据，IO等）是`非常耗时`的，如果一直单线程的`堵塞`下去会导致程序的等待时间过长页面失去响应，影响用户体验了。

如何去解决这个问题呢，我们可以这么想。`耗时`的我们都扔给`异步`去做，做好了再通知下我们做完了，我们拿到数据继续往下走。

接下来利用`setTimeout`来模拟一个异步回调，场景就是：task1是一个要耗时很长的一个请求，task2需要用到task1请求回来的数据，而task3却跟这两个任务没有任何关系，想达到的效果就是，task1执行完之后task2再执行，但是task3可以绕过task1和task2先执行。修改代码：
```js
function task1(task){
    setTimeout(() => {
        console.log('task1');
        task();
    }, 3000);
}
function task2(){
    console.log('task2');
}
function task3(){
    console.log('task3');
}
function testAsync(){
    task1(task2);
    task3();
}
// task3 然后等待3秒
// task1
// task2
```
上面的代码从上往下依次执行，由于task1里面`setTimeout是一个异步方法`，浏览器会`单开一个线程`去执行，所以`先会去执行下面的task3同步任务`，具体原理可参考[JavaScript事件循环机制](http://blog.alanwu.site/2020/03/06/eventLoop/)。

> 继续回到异步回调上来，在执行完任务执行时间超长的task1后，紧接着执行task2，那假如task2也是一个耗时很长的任务并且需要在其执行完之后拿到其执行完的结果呢，那是不是要往task2里面再加一个回调呢？那造成的结果可能就是传说中的`地狱回调`了，想想就害怕，所以`Promise`它带着解决方案来了！！！
## Promise
Promise的思想是， `每一个异步任务返回一个Promise对象`，该对象有一个then方法，允许指定回调函数。 Promises的出现大大改善了异步变成的困境，避免出现回调地狱，嵌套层级得到改善。他有如下API:
- Promise.resolve()
- Promise.reject()
- Promise.prototype.then()
- Promise.prototype.catch()
- Promise.all()  // 所有的完成
- Promise.race() // 竞速，完成一个即可

传送门阮一峰大神的[ECMAScript 6入门](http://es6.ruanyifeng.com/#docs/promise)

为了使代码简介，promise的rejected状态的相关reject()和catch()方法省略：
```js
task1(){
    return new Promise((resolve,reject)=>{
        setTimeout(() => {
            console.log('task1执行了');
            resolve('task1');
        }, 3000);
    })
},
task2(result){
    return new Promise((resolve,reject)=>{
        setTimeout(() => {
            console.log('task2执行了');
            resolve('task2收到了task1的返回值了，他的值是'+result);
        }, 3000);
    })
},
task3(){
    console.log('task3');
},
testAsync(){
    this.task1().then(res=>{
        return this.task2(res);
    }).then(res=>{
        console.log(res)
    });
    this.task3();
}
// task3 等待3秒
// task1执行了 等待三秒
// task2执行了
// task2收到了task1的返回值了，他的值是task1
```
> 由此Promise对象还是很好用的，对于异步的流程的控制得到了大大改善，通过.then()的方法可进行链式调用。 可是 .then() .catch() 的使用也导致代码非常难看，嵌套也很深，所以async/await就出来了！！！
## Async/await
Async/await 是Javascript编写异步程序的新方法。以往的异步方法无外乎回调函数和Promise。但是Async/await建立于Promise之上。直接上代码吧！
```js
task1(){
    return new Promise((resolve,reject)=>{
        setTimeout(() => {
            console.log('task1执行了');
            resolve('task1');
        }, 3000);
    })
},
task2(result){
    return new Promise((resolve,reject)=>{
        setTimeout(() => {
            console.log('task2执行了');
            resolve('task2收到了task1的返回值了，他的值是'+result);
        }, 3000);
    })
},
task3(){
    console.log('task3');
},
// 修改的地方
async testAsync(){
    const res1 = await this.task1();
    const res2 = await this.task2(res1);
    console.log(res2);
    this.task3();
}
// 等待三秒
// task1执行了 等待三秒
// task2执行了
// task2收到了task1的返回值了，他的值是task1
// task3
```
上文中的`promise `实现方法是通过`then的链式调用`，但是采用`async`会更加简洁明了，但是结果发生变化了：`用同步的书写方式实现了异步的代码`。
> Async/await使得异步代码变的不再明显也是一点弊端咯，不过根据实际情况选择最合适的异步编程才是最好的选择。`async 是 Generator 函数的语法糖`。所以想更深入的理解其中内部原理的赶紧去看看 Generator 函数吧。
## 总结
Async/await是近些年来JavaScript最具革命性的新特性之一。他让读者意识到使用Promise存在的一些问题，并提供了自身来代替Promise的方案。
当然，对这个新特性也有一定的担心，体现在：
他使得异步代码变的不再明显，我们好不容易已经学会并习惯了使用回调函数或者.then来处理异步，新的特性当然需要时间成本去学习和体会。