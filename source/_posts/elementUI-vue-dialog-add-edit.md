---
title: 基于vue+elementUI写一个增改的弹框
author: 11LuckyBOY
index_img: 'https://hybridsim.com/wp-content/uploads/2021/04/Activate-Dialog-SIM-Card.jpg'
category: 技术
tags:
  - Vue
  - ElementUI
excerpt: 来写一个可以粘贴复制的弹框吧，在业务中，增改需求还是很常见的，那增改的弹框最好还是封装成一个组件吧，新增则穿空值给组件，修改则传当前值给组件...
date: 2022-01-04 14:18:11
---
> `增删改查`在任何一款应用都是很常见的业务需求，特别是后台管理系统，对于`增删`操作，前端大多都是设计成`弹框`的形式，通过赋值到弹框中来实现对应的操作，简介了当，下面写一个可以复制粘贴的弹框模板以供使用。

![](dialog.gif)

## 设计思路
1. 增删操作都使用`同一个弹框`，弹框是一个公共的组件，接收传值并显示
2. 增操作，父组件传递`空值`给弹框组件
3. 删操作，父组件传递`选中值`给弹框组件
4. 增删接口操作都是在弹框中进行，按照`传递值`来判断调用`哪个接口`
5. 接口调用成功，操作父组件去执行操作（刷新接口）

## 新建弹框组件
组件是一个`el-dialog`弹框，它接收一个对象参数，主要通过对象参数中的`visible`字段来控制显示和隐藏弹框，`dataId`字段来判断调用哪个接口，若`dataId`有值，则是修改接口，反之则是新增接口。最后通过`reloadtable`方法触发父组件的业务逻辑。
```js
<template>
  <div>
    <el-dialog
      :visible.sync="dialog.visible"
      :close-on-click-modal="false"
      :title="title"
      width="1200px"
    >
      ...
    </el-dialog>
  </div>
</template>
<script>
export default {
  props: {
    dialog: {
      type: Object,
      required: true,
      default() {
        return {
          visible: false,
          dataId: null,
          // ...
        }
      }
    }
  },
  data(){
    return {
      form: {
        configName: null,
        configId: null,
        // ...
      }
    }
  },
  computed: {
    title() {
      return this.dialog.dataId ? '修改' : '新增'
    }
  },
  watch: {
    'dialog.visible'(val) {
      if (!val) {
        return
      }
      this.resetForm('form')
      this.form.configName = this.dialog.configName
      this.form.configId = this.dialog.configId
      // ...
    }
  },
  methods: {
    // 提交
    submitForm() {
      if (this.dialog.dataId) {
        // console.log('修改');
        this.dialog.visible = false
        this.$emit('reloadtable')
      } else {
        // console.log('新增');
        // this.dialog.visible = false
        // this.$emit('reloadtable')
      }
    }
  }
}
</script>
```

## 父组件使用
```js
<Dialog :dialog="dialog" @reloadtable="handleQuery" />

data() {
  return {
    dialog: {
      visible: false,
      dataId: null,
      configName: null
    }
  }
},
// 新增
handleAdd() {
  this.dialog.visible = true
  this.dialog.dataId = null
  this.dialog.configName = null
},
// 修改
handleEdit() {
  this.dialog.visible = true
  this.dialog.dataId = this.selectItems[0].id
  this.dialog.configName = this.selectItems[0].configName
  // ...
},
handleQuery() {
  // 操作
},
```