---
uuid: 4bade91d-4665-7387-842d-cd69d1c0977f
title: JavaScript数组循环的几种写法
categories: Javascript
tags: 循环数组
---
## 前言
好用的Javascript循环数组方法可以帮助您编写更加声明性、流畅的风格代码。而不是积累起来for循环和嵌套来处理列表和集合中的数据，您可以利用这些方法更好地将逻辑组织成功能的构建块，然后将它们链接起来以创建更可读和更易于理解的实现。
## 需求
对原数组数据进行处理，返回一个符合需求的新的数组，并且不修改原数组数据。
```js
let studentList = [
    {
        id : '01',
        name : 'vanlus',
        age : 18
    },
    {
        id : '02',
        name : 'James',
        age : 22
    },
    {
        id : '03',
        name : 'Kobe',
        age : 35
    },
]

// 需要获取数组里面的age字段+2组成一个新的数组
// [ 20, 24, 37 ]
```
## for循环
使用率最高，也是最基本的一种遍历方式
```js
let ageList = [];
for(let i=0; i<studentList.length; i++){
    ageList.push(studentList[i].age + 2);
}
```
## forEach循环
forEach中传入要执行的回调函数，函数有三个参数。第一个参数为数组元素(必选)，第二个参数为数组元素索引值(可选)，第三个参数为数组本身(可选)
```js
let ageList = [];
studentList.forEach((item,index,arr)=>{
    console.log(item);
    console.log(index);
    console.log(arr);
    ageList.push(item.age + 2);
})
```
## for in循环
for...in循环可用于循环对象和数组,推荐用于循环`对象`,可以用来遍历JSON
```js
let ageList = [];
for(let index in studentList){
    ageList.push(studentList[index].age + 2);
}
```
## for of循环
可循环数组和对象，推荐用于遍历`数组`。
```js
let ageList = [];
for(let item of studentList){
    ageList.push(item.age + 2);
}
```
for...of提供了三个新方法：
- key()是对键名的遍历；
- value()是对键值的遍历；
- entries()是对键值对的遍历；

```js
let ageList = [];
for(let index of studentList.keys()){
    console.log(index);  // 0,1,2
    ageList.push(studentList[index].age + 2);
}
```
```js
let ageList = [];
for(let [index,value] of studentList.entries()){
    console.log(index); // 0,1,2
    ageList.push(value.age + 2);
}
```
## map循环
map() 会`返回一个新数组`，数组中的元素为原始数组元素调用函数处理后的值。
map() 方法按照原始数组元素顺序依次处理元素。
map `不修改`调用它`原数组`的本身。
map()中传入要执行的回调函数，函数有三个参数。第一个参数为数组元素(必选)，第二个参数为数组元素索引值(可选)，第三个参数为数组本身(可选)
```js
let ageList = studentList.map(item=>{
    return item.age + 2;
})
```
## reduce
array.reduce(function callback(accumulator, currentValue, currentIndex, array){
}[, initialValue])
```js
let ageList = studentList.reduce((arr,item)=>{
    arr.push(item.age + 2); // 这里不能直接return,arr.push返回的是数组新的长度
    return arr;
},[])
```
## Array.from()
从一个类似数组或可迭代对象创建一个新的，`浅拷贝`的数组实例。
```js
let ageList = Array.from(studentList,(item)=>{
    return item.age + 2;
})
```
[参考文章：http://caibaojian.com/for-loop.html](http://caibaojian.com/for-loop.html)