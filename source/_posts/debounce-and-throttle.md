---
title: 防抖和节流
author: Revan
date: 2021-01-06 17:07:35
index_img: /img/debounce-and-throttle.jpg
category: 技术
tags:
  - Javascript
excerpt: 在JavaScript中，函数的防抖和节流不是什么新鲜话题，与之对应的文章也数不胜数，面试中也是经常会问到的。但是看再多都不如自己亲自做一遍，写一遍来的通透一些。
---

## 什么是防抖和节流？
这里举个例子，有一个车站，它的任务就是发车，在没有`防抖`和`节流`之前，只要车站进一个乘客，车站就发一趟车，简而言之，该模式就是{% label warning @来一个乘客就发一趟车 %}。这种模式肯定是不行的，假如发车前车站要做很多复杂的准备，那这种模式肯定会使车站炸裂的。那么`防抖`和`节流`就两种模式就产生了。

`防抖`： 给发车这个任务设置一个时间，假如是10分钟，当有一个乘客来车站后，车站开始10分钟计时，如果10分钟内没有乘客再次来车站，那么就发车；反之，如果10分钟内有乘客再次来车站，那么车站将重新10分钟计时，直到10分钟计时期间没有乘客再次来车站时，再发车。

`节流`： 也给发车这个任务设置一个时间，假如是10分钟，当第一个乘客来到车站后，车站开始10分钟计时，10分钟一到，立马发车，这次发车任务就结束了。以后，当又有第一个乘客来到车站后，也是同样的发车规则。

{% note info %}
总结下来就是，`防抖效果`:10分钟内。只要有新的触发产生：从0开始计时。`节流效果`:10分钟内。只要有新的触发产生：无效，除非之前的操作执行完[^1]。
{% endnote %}
## 场景
说了这些，先来看看如何使用？假设我们有一个这样的场景需求：

我们需要监控页面的鼠标移动事件，会频繁的触发 `mousemove` ，而 `mousemove` 里面有着一些`复杂的计算`，我们假定事件里面需要进行`10万次`循环插入数组操作来模拟`复杂计算`。那么就会导致一个严重的`性能问题`。反反复复的进行计算，而实际上 `mousemove` 实在是`太勤劳`了，我们当中不需要那么“`及时`”的触发，否则可能会导致占用其他操作时间，例如我们的`页面卡顿`。于是我们需要引入`防抖`和`节流`。

先来看下我们即将进行的模拟复杂操作（多余的解读：随机生成10万的数字，从大到小排列，最后拼接输出）：
```js
let arr = [];
for (let i = 0; i <= 100000; i++) {
  arr.push(Math.random())
}
arr.sort((x, y) => y - x)
console.log(arr.join())
```
可以看到计算是比较费时的，接下来我们先上代码，`mousemove` 下的展示：
```js
function handle({
    clientY,
    clientX
}) {
    let arr = [];
    for (let i = 0; i <= 100000; i++) {
        arr.push(Math.random())
    }
    arr.sort((x, y) => y - x)
    console.log(arr.join())
    document.body.innerText = `x:${clientY},y:${clientX}`
}
document.addEventListener('mousemove', handle)
```
`解读`：`handle函数`用于处理 `mousemove` 的操作，将鼠标的坐标显示到页面上。可以发现现在页面已经有些卡顿了，我们接下来就借助`防抖`和`节流`来优化目前的页面体验。
## 防抖
```js
function debounce(fn, time) {
    let timer;//记录定时器
    return function(event) {//返回一个新的函数
        clearTimeout(timer)//如果又被触发了，清除上次的定时器
        document.body.innerText = '鼠标停下展示结果'
        timer = setTimeout(() => {//在下一次事件循环触发，并且记录定时器id，以用于需要清理的时候。
            fn(event)
        }, time)
    }
}
document.addEventListener('mousemove', debounce(handle, 100))//传入handle函数，100毫秒后触发
```
页面上展示后可以发现：鼠标一直滑动的时候，页面没动静，但是一旦鼠标停下 100 毫秒以后开始显示内容。
## 节流
```js
function throttle(fn, time) {
    let flag = false;//设置标志位
    return function(event) {
        if (flag) return false;//如果标志位有定时器id，则说明之前有过任务，撤销本次任务。
        flag = true;
        flag = setTimeout(() => {//记录任务id
            fn(event)
            flag = false;//任务执行完毕，标志位还原
        }, time)
    }
}
document.addEventListener('mousemove', throttle(handle, 100))//传入handle函数，100毫秒后触发
```
页面上展示后可以发现：鼠标滑动的时候，页面也一直在计算，不过不管如何滑动，都会按照指定的时间频率来进行，不会大量触发。

当然实现防抖和节流未必只有我展示的方式，代码是灵活的，例如第二种throttle方式：
```js
function throttle2(fn, time) {
    let begin = new Date();//记录时间
    return function(event) {
        if (new Date() - begin < time) return false;//减去时间看看差了多少，不满足时间直接return
        fn(event)
        begin = new Date()//重置本次记录时间，以便于下次进行比对
    }
}
```
## 两者结合
```js
function throttle(fn, time, fn2, time2) {
    let flag = false;//节流标志
    let timer;//防抖定时器标志
    return function(event) {
        clearTimeout(timer)//清理防抖定时器
        if (flag) return false;
        flag = setTimeout(() => {
            fn(event)//页面显示内容需要实时性比较强，不需要大量计算的ui更新放在这里进行
            flag = false;
            timer = setTimeout(() => {//延时执行复杂运算，鼠标不动的时候就可以有时间进行复杂运算更新。
                fn2()
            }, time2)
        }, time)
    }
}

function handle({
    clientY,
    clientX
}) {
    document.body.innerText = `x:${clientY},y:${clientX}`
}

function handle2() {
    let arr = [];
    for (let i = 0; i <= 100000; i++) {
        arr.push(Math.random())
    }
    arr.sort((x, y) => y - x)
    console.log(arr.join())

}
document.addEventListener('mousemove', throttle(handle, 10, handle2,50))
```
以上代码便可很好的解决页面卡顿，鼠标移动同时，需要移动到某个地方进行一个大量计算。
## 总结
这里可以简单总结一下这个场景下防抖和节流的使用:
- `防抖`：适合复杂运算的时候不要让它们过于频繁的执行，等页面“冷静”下来再去执行。
- `节流`：必须要间隔固定时间执行一次，ui界面等高优先级的事情让它去做。

## 参考
[^1]: [防抖与节流](https://juejin.cn/post/6914186870687694855)