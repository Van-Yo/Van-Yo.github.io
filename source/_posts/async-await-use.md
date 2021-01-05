---
title: Async await处理异常问题
author: Revan
date: 2021-01-03 19:49:47
index_img: /img/asyncAwait.jpg
category: 技术
tags:
  - ES7
  - Javascript
excerpt: 在现在的前端项目中，充斥这大量的异步操作，由于async & await的兴起，导致我今天看到小伙伴有一个项目的代码大量充斥着try catch，代码显得过于冗余且不易阅读，因此本文将寻找方法解决这一问题。
---

## 前言
{% note warning %}
使用过async和await的朋友都知道需要做异常处理[^1]，所以就会用到`try catch`，只要异步方法过多时，代码中就会出现很多这样的`try catch`，代码大概如下：
{% endnote %}
```js
getData(type){
    return new Promise((resolve,reject)=>{
        setTimeout(()=>{
            type == '01' ? resolve('resolve') : reject('reject');
        },3000)
    })
},
async asyncAwait(){
    try{
        let res = await this.getData('01');
        console.log(res);
    }catch(err){
        console.log(err);
    }

    try{
        let res = await this.getData('02');
        console.log(res);
    }catch(err){
        console.log(err);
    }

    // ...
}
```

## 解决方案
在复杂的业务中，这种充斥很多的`try catch`我实在受不了。势必会想办法解决一下这个问题。
首先需要明确的是`await`后面的`promise`只有是一个`resolve`状态，才能正确的拿到其结果。那么要解决这个问题，势必要让异步返回一个`resolve`状态，但是错误我们不能视而不见，结合`nodejs`中`错误优先`，我们可以将错误和结果`封装`成一个数组返回，那么就有了如下代码了：
```js
getData(type){
    return new Promise((resolve,reject)=>{
        setTimeout(()=>{
            type == '01' ? resolve('resolve') : reject('reject');
        },3000)
    })
},
/**
    * 高级的处理,接收一个promise，返回一个resolve状态的promise
    * 
    * @param {*} promise 
*/
dataHandler(promise){
    return promise.then(res=>[undefined,res]).catch(err=>[err,undefined])
},

async asyncAwait(){
    let err,res;
    [err,res] = await this.dataHandler(this.getData('01'));
    if(err){
        console.log('1报错');
    }else{
        console.log('业务处理1');
    }

    [err,res] = await this.dataHandler(this.getData('02'));
    if(err){
        console.log('2报错');
    }else{
        console.log('业务处理2');
    }
}
```

## 结尾
经过这样一转换，你可以优先判断有没有错误，然后再处理你的业务。

## 参考
[^1]: [说一说如何解决async await处理异常问题](https://juejin.cn/post/6912112597596635150)
