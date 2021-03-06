---
title: Js基础知识加强排
author: Revan
index_img: /img/js-enhance.png
category: 技术
tags:
  - Javascript
excerpt: 无聊的时候就来看看Js基础吧，其中会包含很多面试题，都是一些比较难懂易忘且非常重要的知识点，男女老幼皆宜，看完即了解，了解即理解，理解即掌握，掌握即飞跃：D
date: 2021-03-03 15:47:48
---
## 手写代码
### 实现函数能够深度克隆基本类型
`JS` 中的`浅拷贝`与`深拷贝`，只是针对复杂数据类型（`Object`，`Array`）的复制问题。`浅拷贝`与`深拷贝`都可以实现在已有对象上再生出一份的作用。但是对象的实例是存储在`堆内存`中然后通过一个`引用值`去操作对象，由此拷贝的时候就存在两种情况了：`拷贝引用`和`拷贝实例`，这也是`浅拷贝`和`深拷贝`的区别。注：有的数组自带方法是局限性的，例如`slice()`、`concat()`，仅适用于对不包含引用对象的`一维数组`的深拷贝。

**数组的深拷贝**
- `slice()`（有上述局限）
- `concat()`（有上述局限）
- `扩展运算符`（有上述局限）

**对象的深拷贝**
- 利用 `JSON` 对象中的 `parse` 和 `stringify`
- 扩展运算符（有上述局限）
- 利用`递归`来实现每一层都重新创建对象并赋值

```js
function deepCopy(obj) {
  if (typeof obj === 'object') {
    var result = obj.constructor === Array ? [] : {};
    
    for (var i in obj) {
      result[i] = typeof obj[i] === 'object' ? deepCopy(obj[i]) : obj[i];
    }
  } else {
    var result = obj;
  }
  
  return result;
}
```

### 函数中的arguments是数组吗？类数组转数组的方法了解一下？
函数中的`arguments`是类数组:
```js
function info(name,age,address){
    return arguments
}
let arg = info(1,2,3)
console.log(arg);
```
结果：
```js
{
    0:1,
    1:2,
    2:3,
    length:3
}
```
类数组转数组的方法：
- 扩展运算符
- `Array.from`
- `Array.prototype.slice.apply(arguments)`

### 原型、原型链、继承
详见[JavaScript深入之从原型到原型链到继承](https://van-yo.github.io/2021/03/05/js-prototype/)

