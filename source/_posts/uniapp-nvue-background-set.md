---
title: uniapp中nvue背景图设置问题
author: wrrrrrr
index_img: 'https://cdn-media-1.freecodecamp.org/ghost/2019/03/vueart.png'
category: 技术
tags:
  - Uniapp
excerpt: 在做uniapp视频拉流项目中，需要用到uniapp中的nvue语法，但是在设置页面背景图的时候，app端出现一些bug
date: 2022-04-24 15:52:36
---

## 问题
> `nvue` 不支持背景图。但可以使用 `image` 组件和层级来实现类似 `web` 中的背景效果。因为原生开发本身也没有 `web` 这种背景图概念。

这是 `uniapp` 官方的解释，查看网上的解决方案：
使用 `image` 标签 然后给他的 `src` 设置你要设置的背景图的路径，然后给他使用定位的方法
```
<list class="flex1 bg-white Bjt" >
	<cell class="flex1" @tap="board">
		<image class="flex1" style="position: absolute; left: 0; top: 0; right: 0; bottom: 0;" src="../../static/bjt.jpg"></image>  
	</cell>
</list>
```

## 尝试
html
```
<view class="outer">
	<view class="inner">111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111</view>
	<image class="img" src="../../static/light-home-bg.png"></image>
</view>
```

css
```
.outer {
	width: 750rpx;
	height: 1400rpx;
	position: relative;
	z-index: 0;
}
.inner {
	background-color: #585858;
}
.img {
	position: absolute;
	left: 0;
	top: 0;
	right: 0;
	bottom: 0;
	z-index: -1;
}
```

结果背景图片把 `inner` 中的内容都挡住了，也就无法作为背景图了...

## 解决方案
`inner` 再使用 `fixed` 定位再配合 `uniapp` 中的 `scroll-view` 组件实现内容置上，图片置下，并且内容可以滑动。
html
```
<view class="outer">
	<scroll-view scroll-y="true" class="inner">111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111</scroll-view>
	<image class="img" src="../../static/light-home-bg.png"></image>
</view>
```

css
```
.outer {
	width: 750rpx;
	height: 1400rpx;
	position: relative;
	z-index: 0;
}
// .inner {
// 	background-color: #585858;
// }
.inner{
	background-color: #585858;
	position: fixed;
	left: 0;
	right: 0;
	top: 0;
}
.img {
	position: absolute;
	left: 0;
	top: 0;
	right: 0;
	bottom: 0;
	z-index: -1;
}
```