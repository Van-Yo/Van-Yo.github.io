---
uuid: cfc02b61-7966-f280-51ca-eb451debe0a4
title: 在Vue中轻松使用svg图标
date: 2020-12-15 14:36:12
index_img: /img/svg.png
categories: 技术
tags: 
- vue
- svg
---

> 在项目中引入本地图片作为图标的时候，总需要查找文件路径，有时`../../`需要写很长，就显得很不专业也不方便，如果能像`element-ui`那样直接`<el-icon name="icon-file-name"></el-icon>`就好了:D

## 需求
<div align=center>
    <img src="target.gif" />
</div>
首先能像element-ui一样简单以组件的方式引入图标，其次能向上图一样列出svg图库，鼠标点击就能够复制该图标使用的代码，如下：
``` js
<svg-icon :icon-class="18"/>
```

## svg雪碧图
网上搜寻了一圈，一个简单的解决方案是——`svg雪碧图`。它的`工作原理`是: **利用svg的symbol元素，将每个icon包括在symbol中，通过use元素使用该symbol.**



