---
title: Vue不太会用的小知识
author: 11LuckyBOY
index_img: https://cdn-media-1.freecodecamp.org/ghost/2019/03/vueart.png
category: 技术
tags:
  - Vue
excerpt: 记录下Vue中很实用却不怎么会用的小知识（长期更新）
date: 2021-12-03 14:23:02
---
### .sync修饰符
{% note info %}
父子组件传值，用的最多最熟的就是 `props` 和 `$emit` 了，并且子组件不能直接修改父组件传过来的值。假如需要在子组件中直接修改父组件的值，改完之后也不需要 `$emit` 也能传递给父组件，需要怎么写呢？
{% endnote %}

![](sync.gif)

父组件 `:state.sync="state"` :
```js
<template>
  <div class="container-area">
    <ChangeState :state.sync="state" />
    <div>父组件：{{ state }}</div>
  </div>
</template>

<script>
// 子组件
import ChangeState from './components/ChangeState.vue'
export default {
  components: { ChangeState },
  data() {
    return {
      state: true
    }
  }
}
</script>
```

子组件 `this.$emit('update:state', !this.state)` ：
```js
<template>
  <div class="change-state">
    子组件：{{ state }}
    <el-button @click="change">切换</el-button>
  </div>
</template>

<script>
export default {
  name: 'ChangeState',
  props: {
    state: {
      type: Boolean,
      default: true
    }
  },
  methods: {
    change() {
      this.$emit('update:state', !this.state)
    }
  }
}
</script>
```

### 重置data的数据为初始状态
{% note info %}
Vue中重置data的数据为初始状态，用的最多最笨的方法就是一个一个去重新赋值，那有没有什么便捷高效的方法呢？试试`Object.assign(this.$data, this.$options.data())`吧
{% endnote %}

栗子：通过 `change()` 方法修改 `data` 中的值，再通过 `reset()` 方法重置 `data` 的数据为初始状态
![](rset.gif)
```js
<template>
  <div class="container-area">
    <div>姓名:{{ name }}</div>
    <div>年龄:{{ age }}</div>
    <div>等级:{{ level }}</div>
    <div>
      <el-button @click="change">改变</el-button>
      <el-button @click="reset">重置</el-button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      name: '11LuckyBOY',
      age: 13,
      level: 1
    }
  },
  methods: {
    change() {
      this.name = 'revan'
      this.age = 18
      this.level = 5
    },
    reset() {
      // alert(JSON.stringify(this.$data))
      // alert(JSON.stringify(this.$options.data()))
      Object.assign(this.$data, this.$options.data())
    }
  }
}
</script>
```
`this.$data` 获取当前状态下的 `data`
`this.$options.data()` 获取该组件初始状态下的 `data`
所以，下面就可以将初始状态的data复制到当前状态的 `data` ，实现重置效果：`Object.assign(this.$data, this.$options.data())`
当然，如果你只想重置 `data` 中的某一个对象或者属性：`this.form = this.$options.data().form`

### watch监听对象中的某个属性值
监听对象`dialog`中属性值`visible`
```js
watch: {
  'dialog.visible'(val) {
    // 业务逻辑
  }
},
```

### 策略模式
策略模式的定义是：定义一系列的算法，把它们一个个封装起来，并且使它们可以相互替换，属于 `javascript` 内容，暂放本文，平时项目中优先使用。 使用策略模式的优点如下：
1. 策略模式利用组合，委托等技术和思想，有效的避免很多if条件语句
2. 策略模式提供了开放-封闭原则，使代码更容易理解和扩展
3. 策略模式中的代码可以复用

`案例`：
比如公司的年终奖是根据员工的工资和绩效来考核的，绩效为 `A` 的人，年终奖为工资的4倍，绩效为 `B` 的人，年终奖为工资的3倍，绩效为 `C` 的人，年终奖为工资的2倍；现在我们使用一般的编码方式会如下这样编写代码：
```js
var calculateBouns = function(salary,level) {
    if(level === 'A') {
        return salary * 4;
    }
    if(level === 'B') {
        return salary * 3;
    }
    if(level === 'C') {
        return salary * 2;
    }
};
// 调用如下：
console.log(calculateBouns(4000,'A')); // 16000
console.log(calculateBouns(2500,'B')); // 7500
```
代码缺点如下：
1. `calculateBouns` 函数包含了很多`if-else`语句。
2. `calculateBouns` 函数缺乏弹性，假如还有D等级的话，那么我们需要在 `calculateBouns` 函数内添加判断等级 `D` 的 `if` 语句；
3. 算法复用性差，如果在其他的地方也有类似这样的算法的话，但是规则不一样，我们这些代码不能通用。

使用 `Javascript` 版本的 `策略模式` 重构代码
```js
var obj = {
    "A": function(salary) {
        return salary * 4;
    },
    "B" : function(salary) {
        return salary * 3;
    },
    "C" : function(salary) {
        return salary * 2;
    } 
};
var calculateBouns =function(level,salary) {
    return obj[level](salary);
};
console.log(calculateBouns('A',10000)); // 40000
```