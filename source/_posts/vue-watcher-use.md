---
uuid: 23d5d2bc-46a3-8f6a-e2a3-3f36e1afb6f5
title: vue中watch如何正确使用
date: 2020-11-26 14:38:00
categories: 技术
tags:
- vue
- Javascript
---
## 问题
之前写vue项目的时候,涉及到`watch`监听`props`中的数据时，总产生一个未知的bug,利用`console log`去排除问题，却找不出问题所在。举例：
``` js
// 父组件（部分代码省略）
<template>
    <section>
        <ListCom :listArr="list"></ListCom>
    </section>
</template>

<script>
export default {
    data() {
        return {
            list : []
        };
    },
    created(){
        setTimeout(()=>{
            this.list.push({name:'vanlus',level:'001'})
        },3000)
    }
};
</script>
```

``` js
// 子组件
<template>
    <section class="template-area">
        <ul>
            <li v-for="(item,index) in listArr" :key="index">{{item.name}}</li>
        </ul>
    </section>
</template>

<script>
export default {
    props :{
        listArr : {
            type : Array
        }
    },
    watch:{
        listArr : {
            handler(newVal,oldVal){
                if(newVal != oldVal){
                    console.log('变化了');
                    console.log(newVal);
                    console.log(newVal[0].name);
                }
            },
            deep :true,
            immediate : true
        }
    }
};
</script>
```
这时候，F12查看控制台，报错了：
```
[Vue warn]: Error in callback for immediate watcher "listArr": "TypeError: Cannot read property 'name' of undefined"
```
再去`console log` 看一下输出了什么？
``` js
// 变化了
// [{name:'vanlus',level:'001'}]
```
这就很奇怪了，明明有值，为什么还报错。

## 原因
> 在WebKit中,JavaScript中的console.log函数是异步的

浏览器F12打开控制台,分别敲下面这两段代码,并且拉开显示全部结果，你就会发现问题了：
``` js
var arr = [1,2,3,4]
console.log(arr)
arr.push(5)
console.log(arr)
```
![01](01.png)
展开
![02](02.jpg)

显示的不一样，但拉开发现结果是一样的，这是为什么呢？《JavaScript异步编程》书中是这么解释的：
> WebKit的console.log并没有立即拍摄对象快照，相反，它只存储了一个`指向对象的引用`，然后在代码返回事件队列时才去拍摄快照。而chrome的内核正是webkit；Node的console.log是另一回事，它是严格同步的，因此同样的代码输出却是正确的。
> 书中指出，JavaScript 环境提供的异步函数一般分为两大类：`I/O函数`和`计时函数`。console.log就是一个I/O函数。对于引用类型，console.log会先储存一个引用，因此在打印`引用类型`时`结果不一定准确`。

所以 console.log 到底是同步还是异步`取决于运行环境`。

## 解决方案
对于`引用类型`的值，父组件中data里面有`初始值`，并且`值发生变化`再传输给子组件并使用该值时，建议这么判断。
![desc](desc.jpg)
``` js
watch:{
    listArr : {
        handler(newVal,oldVal){
            // 可以在这里判断，判断准则看下面总结
            if(newVal.length){
                console.log(newVal[0].name);
            }
        },
        deep :true,
        immediate : true
    }
},
```
## 总结
父子组件传值过程大概描述如下图，`子组件`没有default值，`父组件`有一个初始化值（`引用类型`），而后又再次请求设置了一个新值。
![res](res.jpg)

组件传值以及监听流程如下（`deep`和`immediate`都为`true`）：
1.第一次，监听的值发生改变，前后值分别是`undefined`和`[]`,能触发watch中的handler函数，并且`前后值确实不一样`，这时候监听的值等于`一个引用类型地址`，而不仅仅等于一个空数组：`[]`；
2.父组件中的值发生变化；
3.第二次，监听的值发生改变, 前后值分别是`[]`和`['小明','小红']`,能触发watch中的handler函数,但是由于两者是`同一个引用类型地址`，所以此时两者的值是相等的。

这也就解释了为什么最上面代码报错，因为发生在第一次，数据还没到位。但是却能打印出值，这是因为，console log异步给造成的一种错觉：
``` js
if(newVal != oldVal){
    console.log('变化了');
    console.log(newVal);
    console.log(newVal[0].name);
}
```
未完待续 :D
