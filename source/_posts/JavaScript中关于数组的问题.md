---
uuid: d1b6ec50-fd2c-9954-662b-93a51e65db94
title: JavaScript中关于数组的问题
categories: 技术
tags: 
- Javascript
---
前端工作中，关于js数组的操作也占据了很大的一部分，特别是关于数组的操作上。下面我就以前端中常用的操作，说说js中数组有哪些方法开始说起。也有很多人会迷惑，哪些是改变原数组，哪些不会改变呢？希望对己对人有帮助:D
## 前言
JavaScript中创建数组有两种方式，第一种是构造函数的方式，第二种是字面量的方式。

构造函数
```js
var arr1 = new Array();
var arr2 = new Array(10);
var arr3 = new Array(1,2,3,4,5);
```
使用数组字面量
```js
var arr4 = [];
var arr5 = [1,2,3,4,5];
```
两者的区别

使用new关键字的方法会在内存中开辟一些空间，用来记录与存储该变量，也就是这是一个实例化过程。在Javascript里分配大量的new变量地址是一项很慢的操作。Array()是一个对象，[]是一个数据原型。使用new Array()系统每次都会新生成一个对象，浏览器每生成一个对象都会耗费资源去构造他的属性和方法。

其实这和字符串的创建差不多，new String(‘str’)，一种是直接创建了一个字符串，一个是调用字符串的构造函数创建字符串对象然后再创建这个字符串，中间多了一个创建对象的过程。所以为了效率起见，推荐使用字面量的方式创建。接下来介绍各个数组方法。

## push()和pop()
push(): 把元素逐个添加到数组末尾，可接收任意数量的参数并返回修改后数组的长度。

pop()：数组末尾移除最后一项，减少数组的 length 值，然后返回移除的项。

```js
var arr = ["a","b","c"];
var count = arr.push("d","e");
console.log(count); // 5
console.log(arr); // ["a", "b", "c", "d", "e"]
var item = arr.pop();
console.log(item); // e
console.log(arr); // ["a", "b", "c, "d"]
```
## shift() 和 unshift()
shift()：删除原数组第一项，并返回删除元素的值；如果数组为空则返回undefined 。

unshift():将参数添加到原数组开头，并返回数组的长度 。

```js
var arr = ["a","b","c"];
var count = arr.unshift("d","e");
console.log(count); // 5
console.log(arr); //["d", "e", "a", "b", "c"]
var item = arr.shift();
console.log(item); // d
console.log(arr); // ["e", "a", "b", "c"]
```
## sort()
sort()：按升序排列数组项，即最小的值位于最前面，最大的值排在最后面。

但是注意的是，在排序时，sort()方法会调用数组项的 toString()方法，然后比较得到的字符串来确定如何排序。
```js
var arr1 = ["a", "d", "c", "b"];
console.log(arr1.sort()); // ["a", "b", "c", "d"]
arr2 = [18, 29, 50, 4];
console.log(arr2.sort()); // [18, 29, 4, 50]
console.log(arr2); // [18, 29, 4, 50]
```
sort()方法可以接收一个比较函数作为参数，若参数比较返回-1则是升序排序，返回1则是升序排序。
```js
function compare(value1, value2) {
    if (value1 < value2) {
        return -1;
    } else if (value1 > value2) {
        return 1;
    } else {
        return 0;
    }
}
arr2 = [18, 29, 4, 50];
console.log(arr2.sort(compare)); // [4, 18, 29, 50]
```
如果是降序的话，只需要交换返回值就好
```js
function compare(value1, value2) {
    if (value1 < value2) {
        return 1;
    } else if (value1 > value2) {
        return -1;
    } else {
        return 0;
    }
}
arr2 = [18, 29, 50, 4];
console.log(arr2.sort(compare)); // [50, 29, 18, 4]
```
## reverse()
reverse()：反转数组项的顺序

```js
var arr = [18, 29, 50, 4];
console.log(arr.reverse()); //[4, 50, 29, 18]
console.log(arr); //[4, 50, 29, 18]
```
## join()
默认用逗号为分隔符，参数是分隔符。

```js
var arr = [1,2,3]; 
console.log(arr.join()); // 1,2,3 
console.log(arr.join("+")); // 1+2+3 
console.log(arr); // [1, 2, 3]
```
## concat()
concat() ：将参数添加到原数组中。该方法会先创建当前数组一个副本，然后将接收到的参数添加到这个副本的末尾，最后返回新构建的数组。若没有给concat()方法传递参数，默认情况下复制当前数组并返回，不会改变原数组。

```js
var arr = [1,2,3,4];
var arrCopy = arr.concat(5,[6,7,8]);
console.log(arrCopy); //[1, 2, 3, 4, 5, 6, 7, 8]
console.log(arr); // [1, 2, 3, 4]
```
## slice()
slice()：返回从原数组中指定开始下标到结束下标之间的项组成的新数组。slice()方法可以接受一或两个参数，即要返回项的起始和结束位置。在只有一个参数的情况下， slice()方法返回从该参数指定位置开始到当前数组末尾的所有项。如果有两个参数，该方法返回起始和结束位置之间的项，但不包括结束位置的项。

```js
var arr = [1,3,5,7,9,11];
var arrCopy = arr.slice(1);
var arrCopy2 = arr.slice(1,4);
var arrCopy3 = arr.slice(1,-2);
var arrCopy4 = arr.slice(-4,-1);
console.log(arr); //[1, 3, 5, 7, 9, 11](原数组没变)
console.log(arrCopy); //[3, 5, 7, 9, 11]
console.log(arrCopy2); //[3, 5, 7]
console.log(arrCopy3); //[3, 5, 7]
console.log(arrCopy4); //[5, 7, 9]
```
- arrCopy只设置了一个参数，也就是起始下标为1，所以返回的数组为下标1（包括下标1）开始到数组最后。
- arrCopy2设置了两个参数，返回起始下标（包括1）开始到终止下标（不包括4）的子数组。
- arrCopy3设置了两个参数，终止下标为负数，当出现负数时，将负数加上数组长度的值（6）来替换该位置的数，因此就是从1开始到4（不包括）的子数组。
- arrCopy4两个参数都是负数，所以都加上数组长度6转换成正数，因此相当于slice(2,5)。

## splice()
splice()：数组中最强大的方法，可以实现多种操作。
- 添加：向数组中添加项，参数：起始位置、 0（要删除的项数）和要插入的项。例如，splice(2,0,1,1)会从当前数组的位置 2 开始插入1和1。
- 删除：删除数组中的项，参数：要删除的第一项的位置和要删除的项数。例如， splice(0,2)会删除数组中的前两项。
- 修改：向数组中添加项，且同时支持删除数组中的项，参数：起始位置、要删除的项数和要插入的项。例如，splice (2,1,1,1)会删除当前数组位置 2 的项，然后再从位置 2 开始插入1和1。
- splice()方法返回一个数组，该数组中包含从原始数组中删除的项，如果没有删除任何项，则返回一个空数组。

```js
var arr = ['a','b','c','d','e','f'];
var arrCut1 = arr.splice(0,2);
console.log(arr); //[ 'c', 'd', 'e', 'f' ]
console.log(arrCut1);//[ 'a', 'b' ]

var arrCut2 = arr.splice(2,0,1,1);
console.log(arr); //[ 'c', 'd', 1, 1, 'e', 'f' ]
console.log(arrCut2); //[]

var arrCut3 = arr.splice(1,1,2,2);
console.log(arr); //[ 'c', 2, 2, 1, 1, 'e', 'f' ]
console.log(arrCut3); //[ 'd' ]
```
## indexOf()和 lastIndexOf()
indexOf()：接收两个参数：要查找的项和（可选的）表示查找起点位置的索引。其中， 从数组的开头（位置 0）开始向后查找。

lastIndexOf：接收两个参数：要查找的项和（可选的）表示查找起点位置的索引。其中， 从数组的末尾开始向前查找。

若没有查找到则返回-1。
```js
var arr = ['a','b','c','d','e','e','a'];
console.log(arr.indexOf('c')); //2
console.log(arr.lastIndexOf('c')); //2
console.log(arr.indexOf('e',4)); //4
console.log(arr.lastIndexOf('e',4)); //4
console.log(arr.indexOf("aa")); //-1
```
## forEach()
forEach()：对数组进行遍历循环，对数组中的每一项运行给定函数。这个方法没有返回值。参数都是function类型，默认有参数，参数分别为：遍历的数组内容；对应的数组索引，数组本身。
```js
var arr = [1, 2, 3, 4, 5];
arr.forEach(function(x, index, a){
    console.log(x + ',' + index + ',' + (a === arr));
});
// 输出为：
1,0,true
2,1,true
3,2,true
4,3,true
5,4,true
```
关于forEach是否可以改变原数组的问题：
```js
//这样操作发现是改变不了的
let oldArr = [1,2,3,4,5] ;
oldArr.forEach(item => {
    item = 1
})
console.log(oldArr);//[1, 2, 3, 4, 5]
```
```js
//这样操作发现是可以改变的
let oldArr = [1,2,3,4,5] ;
oldArr.forEach((item,index) => {
    oldArr[index] = 0;
})
console.log(oldArr); //[ 0, 0, 0, 0, 0 ]
```
其实每次forEach的循环的item，只是forEach给我们在另一个地方复制创建新元素，和原来的元素并无关系。到头来其实是JavaScript的基本数据类型与引用数据类型的区别。对于基本数据类型：Number, String, Boolean, Null, Undefined, Symbol，它们在`栈内存中直接存储变量与值`。Object对象的真正的数据是保存在`堆内存，栈内只保存了对象的变量以及对应的堆的地址`，所以操作Object其实就是直接操作了原数组对象本身。

forEach 的基本原理也是for循环，所以使用arr[index]的形式赋值改变，其实就是操作堆地址，是可以改变的。
## map()
map()：对数组中的每一项运行给定函数，返回每次函数调用的结果组成的数组。
```js
var arr = [1, 2, 3, 4, 5];
var arr1 = arr.map(function(item){
    return item+1;
});
console.log(arr1); //[ 2, 3, 4, 5, 6 ]
```
## filter()
filter()：对数组中的每一项运行给定函数，返回满足过滤条件组成的数组。
```js
var arr = [1, 2, 3, 4, 5, 6]
var arr1 = arr.filter((item) => {
    return item > 2
});
console.log(arr1);//[ 3, 4, 5, 6 ]
```
## every()
every()：判断数组中每一项都是否满足条件，只有所有项都满足条件，才会返回true。
```js
var arr = [1, 2, 3, 4, 5, 6];
var arr2 = arr.every(function(x) {
    return x < 7;
}); 
console.log(arr2); //true
var arr3 = arr.every(function(x) {
    return x < 5;
}); 
console.log(arr3); // false
```
## some()
some()：判断数组中是否存在满足条件的项，只要有一项满足条件，就会返回true。
```js
var arr = [1,2,3,4,5,6];
var arr2 = arr.some(function(x) {
    return x < 6;
}); 
console.log(arr2); //true
var arr3 = arr.some(function(x) {
    return x < 0;
}); 
console.log(arr3); //false
```
## reduce()和 reduceRight()
这两个方法都会实现迭代数组的所有项，然后构建一个最终返回的值。

reduce()方法从数组的第一项开始，依次遍历到最后。而 reduceRight()则从数组的最后一项开始，依次向前遍历到第一项。

方法接收两个参数：一个在每一项上调用的函数和基础的初始值。

reduce()和 reduceRight()的函数接收 4 个参数：前一个值（pre）、当前值（cur）、项的索引（index）和数组对象（array）。这个函数返回的任何值都会作为第一个参数自动传给下一项。第一次迭代发生在数组的第二项上，因此第一个参数是数组的第一项，第二个参数就是数组的第二项。

下面代码用reduce()实现数组求和，数组一开始加了一个初始值10。
```js
var values = [1,2,3,4,5];
var sum = values.reduceRight(function(prev, cur, index, array){
    return prev + cur;
},5);
console.log(sum); //20
```
## 小结
以上的数组方法不论在面试中还是在项目中都非常管用，特别是后端的数据处理的时候会经常用到。现在总结一下哪些方法是改变原数组的，哪些是没有的。值得注意的是forEach的特殊点，文中已做说明。

1.改变原数组：
- push() pop()
- shift() unshift()
- sort() reverse()
- splice()

2.原数组不变：
- concat() join()
- slice() map()
- indexOf() lastIndexOf()
- filter() every()
- some() reduce()
- reduceRight()

参考：http://blog.alanwu.site/2020/03/18/jsArrayAPI/
