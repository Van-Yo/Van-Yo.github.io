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