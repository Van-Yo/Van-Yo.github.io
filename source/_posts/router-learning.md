---
title: 前端路由学习
author: Revan
date: 2021-01-15 15:15:51
index_img: /img/router.jpg
category: 技术
tags:
  - Javascript
  - Html
excerpt: 前端项目写了这么多，但如果有人突然问你，谈谈你对前端路由的理解，可能你第一反应有点蒙圈，所以，还是有必要系统性地学习一下前端路由的，路由对于前端来说还是挺重要的。
---

{% note info %}
假如面试官问你：什么是前端路由？为什么用前端路由？前端路由解决了什么？你该怎么回答呢=.=！
{% endnote %}

## 前言
首先先简单回答一下上面的问题，然后带着问题和答案去学习`前端路由`。

{% cb 什么是前端路由?, true %}
_简单说就是前端控制页面跳转,而不需要要向后端去请求。_

{% cb 为什么用前端路由？, true %}
_还不是因为时代在进步，单页应用的兴起，当然兴起的原因就不细说了。_

{% cb 前端路由解决了什么？, true %}
_后台和前端工作更加细化了，后台主要工作就是提供数据，前端负责展示，当然还有一些节约资源，减小服务器压力什么的。_

## 多页应用和单页应用
多页应用和单页应用有什么区别呢？
### 多页应用
初学前端就知道，一个 `HTML` 就是一个页面，在浏览器输入网址后发起请求，返回来的 `HTML` 页面是最终呈现的效果，`url`类似于以index.html文件名结尾的都是多页应用。并且每次点击页面跳转，都会重新请求 `HTML` 资源。[^1]

举个例子，[博客园](https://www.cnblogs.com/)就是一个多页应用，每次加载页面，都会返回 `HTML` 资源以及里面的 `CSS` 等静态资源，组合成一个新的页面。可以通过查看后端`返回值`和`预览`进行查看：
![](router-1.png)

![](router-2.jpg)

网页上能看到什么图片或文字，你能在上述图片中找到相应的 `HTML` 结构，并且是单个的 `HTML` 文件，那也属于传统页面，也就是 `DOM` 直出。

### 单页应用
时代在进步，科技在发展，面对日益增长的网页需求，网页开始走向模块化、组件化的道路。随之而来的是代码的难以维护、不可控、迭代艰难等现象。面临这种情况，催生出不少优秀的现代前端框架，首当其冲的便是 `React` 、 `Vue` 、 `Angular` 等著名单页面应用框架。而这些框架有一个共同的特点，便是“通过 `JS` 渲染页面”。

举个例子，以前我们直出 `DOM` ，而现在运用这些单页面框架之后， `HTML` 页面基本上只有一个 `DOM` 入口，大致如下所示：
![](router-3.jpg)
所有的页面组件，都是通过运行上图底部的 `main.bundle.js` 脚本，挂载到 `<div id="app"></div>` 这个节点下面。用一个极其简单的 JS 展示挂载这一个步骤：
```html
<body>
  <div id="app"></div>
  <script>
    const root = document.getElementById('app') // 获取根节点
    const divNode = document.createElement('div') // 创建 div 节点
    divNode.innerText = '你妈贵姓？' // 插入内容
    root.appendChild(divNode) // 插入根节点
  </script>
</body>
```
既然单页面是这样渲染的，那如果我有十几个页面要互相跳转切换，咋整！！？？这时候 {% label info @前端路由 %} 应运而生，它的出现就是为了解决`单页应用`，通过切换浏览器地址路径，来匹配相对应的页面组件。我们通过一张丑陋的图片来理解这个过程：
![](router-4.jpg)

`前端路由`会根据浏览器地址栏 `pathname` 的变化，去匹配相应的页面组件。然后将其通过创建 `DOM` 节点的形式，塞入根节点 `<div id="root"></div>` 。这就达到了无刷新页面切换的效果，从侧面也能说明正因为无刷新，所以 `React` 、 `Vue` 、 `Angular` 等现代框架在创建页面组件的时候，每个组件都有自己的 `生命周期` 。

## 原理
`前端路由` 插件比较火的俩框架对应的就是 `Vue-Router` 和 `React-Router` ,但是它们的逻辑，归根结底还是一样的，用殊途同归四个字，再合适不过。通过分析`哈希模式`和`历史模式`的实现原理，让大家对前端路由的原理有一个更深刻的理解。

### 哈希模式
`a` 标签锚点大家应该不陌生，而浏览器地址上 `#` 后面的变化，是可以被监听的，浏览器为我们提供了原生监听事件 `hashchange` ，它可以监听到如下的变化：
- 点击 `a` 标签，改变了浏览器地址
- 浏览器的前进后退行为
- 通过 `window.location` 方法，改变浏览器地址

接下来我们利用这些特点，去实现一个 `hash` 模式的简易路由：
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Hash 模式</title>
</head>
  <body>
    <div>
      <ul>
        <li><a href="#/page1">page1</a></li>
        <li><a href="#/page2">page2</a></li>
      </ul>
      <!--渲染对应组件的地方-->
      <div id="route-view"></div>
    </div>
  <script type="text/javascript">
    // 第一次加载的时候，不会执行 hashchange 监听事件，默认执行一次
    // DOMContentLoaded 为浏览器 DOM 加载完成时触发
    window.addEventListener('DOMContentLoaded', Load)
    window.addEventListener('hashchange', HashChange)
    // 展示页面组件的节点
    var routeView = null
    function Load() {
      routeView = document.getElementById('route-view')
      HashChange()
    }
    function HashChange() {
      // 每次触发 hashchange 事件，通过 location.hash 拿到当前浏览器地址的 hash 值
      // 根据不同的路径展示不同的内容
      switch(location.hash) {
      case '#/page1':
        routeView.innerHTML = 'page1'
        return
      case '#/page2':
        routeView.innerHTML = 'page2'
        return
      default:
        routeView.innerHTML = 'page1'
        return
      }
    }
  </script>
  </body>
</html>
```
当然，这是很简单的实现，真正的 `hash` 模式，还要考虑到很多复杂的情况，大家有兴趣就去看看源码。

### 历史模式
`history` 模式会比 hash 模式稍麻烦一些，因为 `history` 模式依赖的是原生事件 `popstate` ，下面是来自 `MDN` 的解释：
![](router-5.jpg)
{% note primary %}
小知识：`pushState` 和 `replaceState` 都是 `HTML5` 的新 `API`，他们的作用很强大，可以做到改变浏览器地址却不刷新页面。这是实现改变地址栏却不刷新页面的重要方法。
{% endnote %}

包括 `a` 标签的点击事件也是`不会`被 `popstate` 监听。我们需要想个办法解决这个问题，才能实现 `history` 模式。

**解决思路：**我们可以通过遍历页面上的所有 `a` 标签，阻止 `a` 标签的默认事件的同时，加上点击事件的回调函数，在回调函数内获取 `a` 标签的 `href` 属性值，再通过 `pushState` 去改变浏览器的 `location.pathname` 属性值。然后手动执行 `popstate` 事件的回调函数，去匹配相应的路由。逻辑上可能有些饶，我们用代码来解释一下：
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>History 模式</title>
</head>
<body>
  <div>
    <ul>
      <li><a href="/page1">page1</a></li>
      <li><a href="/page2">page2</a></li>
    </ul>
    <div id="route-view"></div>
  </div>
  <script type="text/javascript">
    window.addEventListener('DOMContentLoaded', Load)
    window.addEventListener('popstate', PopChange)
    var routeView = null
    function Load() {
      routeView = document.getElementById('route-view')
      // 默认执行一次 popstate 的回调函数，匹配一次页面组件
      PopChange()
      // 获取所有带 href 属性的 a 标签节点
      var aList = document.querySelectorAll('a[href]')
      // 遍历 a 标签节点数组，阻止默认事件，添加点击事件回调函数
      aList.forEach(aNode => aNode.addEventListener('click', function(e) {
        e.preventDefault() //阻止a标签的默认事件
        var href = aNode.getAttribute('href')
        //  手动修改浏览器的地址栏
        history.pushState(null, '', href)
        // 通过 history.pushState 手动修改地址栏，
        // popstate 是监听不到地址栏的变化，所以此处需要手动执行回调函数 PopChange
        PopChange()
      }))
    }
    function PopChange() {
      console.log('location', location)
      switch(location.pathname) {
      case '/page1':
        routeView.innerHTML = 'page1'
        return
      case '/page2':
        routeView.innerHTML = 'page2'
        return
      default:
        routeView.innerHTML = 'page1'
        return
      }
    }
  </script>
</body>
</html>
```
{% note warning %}
这里注意，不能在浏览器直接打开该静态文件，需要通过 `web` 服务，启动端口去浏览网址。
{% endnote %}

## 总结
这篇文章主要知识点集中在前端路由这块，能完全看完，并且把实现原理捋一遍，我想你应该对现代前端框架会有一个新的理解。

## 参考
[^1]: [你好，谈谈你对前端路由的理解](https://juejin.cn/post/6917523941435113486)