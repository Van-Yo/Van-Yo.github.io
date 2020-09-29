---
uuid: 024aa8cb-7713-505f-b7ef-f4f9ca288f35
title: Vue.js 组件之间的传值
categories: 技术
tags: vue
---
## 概述
vue中组件之间的传值传值情况主要有以下三种：
- 父组件向子组件传值
- 子组件向父组件传值
- 兄弟组件之间相互传值或者是两个没有关系的组件之间的传值

在开始介绍之前我们先建立3个vue文件，文件名分别为:Parent.vue , Child1.vue , Child2.vue。

## 父组件向子组件传值
这种情况是三种传值方案中最简单的, 通过在子组件中使用 props就可以实现。
### 子组件Child1.vue
```js
<template>
    <button > {{btnName}}</button>
</template>
<script>
export default {
  name: 'Child1',
  props: ['btnName']
}
</script>
<style>
    button{
        padding:5px 10px;
        margin:2px;
        background-color:#fff;
        border-radius: 5px; 
    }
</style>
```
### 父组件Parent.vue
```js
<template>
  <div>
    <child-1 :btnName="btnName"/>
  </div>
</template>
<script>
import Child1 from './Child1'

export default {
  name: 'Parent',
  components: {
    'child-1': Child1
  },
  data () {
    return {
      btnName: ' 我是一个button'
    }
  }
}
</script>
```
关键点就是需要在子组件中 使用 props 关键字 来接收父组件传过来的值。
## 子组件向父组件传值
在子组件向父组件传值时需要使用 vue 中的 $on 和 $emit ，使用$emit 可以触发 $on 中监听的事件，现在我们来实现一个可以传入电话号码的功能
### 子组件Child2.vue
```js
<template>
    <button @click="clickHandle"> {{btnName}}</button>
</template>
<script>
export default {
  name: 'Child1',
  props: ['btnName'],
  methods: {
    clickHandle () {
      this.$emit('count','120')
    }
  }
}
</script>
```
在父组件中有个 telNum字段用于显示电话号码。
首先我们需要在Parent.vue的data中定义telNum变量，默认值为'110'，然后将count加入到template中便于显示, 此时parent.vue的template的代码如下：
```js
<template>
  <div>
      <p>传过来的电话号码是: {{telNum}}</p>
      <child-1 :btnName="btnName"/>
  </div>
</template>
<script>
import Child1 from './Child1'

export default {
  name: 'Parent',
  components: {
    'child-1': Child1
  },
  data () {
    return {
      btnName: ' 我是一个button',
      telNum: '110'
    }
  }
}
</script>
```
接下来我们需要在父组件中增加一个可以改变telNum值的事件,同时在 中加上监听事件
```js
<template>
  <div>
      <p>传过来的电话号码是: {{telNum}}</p>
      <child-1 :btnName="btnName" @count="changeCount"/>
  </div>
</template>
<script>
import Child1 from './Child1'

export default {
  name: 'Parent',
  components: {
    'child-1': Child1
  },
  data () {
    return {
      btnName: ' 我是一个button',
      telNum: '110'
    }
  },
  methods:{
      changeCount(value){
          this.telNum = value;
      }
  }
}
</script>
```
现在通过点击button即可实现改变count的值
## 兄弟组件之间的传值
兄弟组件之间传值有两种方式：
- 将需要改变的值放到父组件中，子组件通过props来获取父组件的值(不推荐)
- 通过eventbus 来实现兄弟组件之间的传值，其原理还是通过$on和$emit来时实现的，区别是现在全局建立一个空的vue对象，然后将事件绑定到该空对象上，最后通过该空对象来触发$on监听的事件(不推荐)
- vuex