---
title: vue-cli中同一套代码如何打出两个包
author: 11LuckyBOY
index_img: 'https://i.morioh.com/1656fea560.png'
category: 技术
tags:
  - Vue
  - Scss
excerpt: 最近有一个需求，在现有的基于vue-cli脚手架的项目中打一个新的生产包，要求新的生产包除了项目的登录背景图、logo和公司名字不同，其他和原包全部要相同，该如何做？
date: 2022-01-14 16:36:47
---

## 解决方案
1. 最初想到的就是切新的代码分支，然后手动把项目中的图片和文字给手动修改，然后再打个包。但是想到项目并没有结束，需要一直迭代，如果频繁切分支、合分支再打包实现上述需求，显然不合理（跳过）。
2. 创建一个新的脚本，通过运行环境来判断到底是打包哪个图片、哪个文字。（采用）

## 解决步骤
### 新建环境配置文件
在项目根目录新增`.env.cq`配置文件
```js
// TODO 重庆生产环境

NODE_ENV='chongqing'

# must start with VUE_APP_
VUE_APP_ENV = 'chongqing'
VUE_APP_TITLE='览辉科技'
VUE_APP_BASE_API = '/'
```

### 在package.json中增加脚本
```js
"build:cq": "vue-cli-service build --mode cq"
```

### 在项目中修改
针对文字修改：
```js
computed: {
  nodeEnv() {
    return process.env.NODE_ENV;
  }
},
```
```html
<span class="title">{{
  nodeEnv === 'chongqing' ? '览辉科技' : '南京讯汇'
}}</span>
```

针对图片修改，背景图主要通过动态 `class` 来实现
![](scss-1.jpg)
![](scss-2.jpg)
![](scss-3.jpg)

### 结果
分别运行脚本 `build` 和 `build:cq` 后，运行项目，确实能够实现不同指令打出 `不同样子` 的包，但是发现打出的包大了很多，经过仔细排查，发现打出的两个项目包，静态资源文件完全一致，而不是各自打出各自所需的静态资源，造成A的打包项目中也有B的背景图，B的打包项目中也有A的背景图。而造成该问题的根源就在于上面的动态 `class` 会将两个 `class` 属性内容都进行编译，从而将两张图都打包进去。

## 优化
想不通过动态 `class` 引入背景图的方法，直接在 `scss` 中判断运行环境 `process.env.NODE_ENV` 从而设置哪张背景图片，如何在 `scss` 中使用 `node` 变量呢？

### sass-loader配置全局变量
```js
// 可直接在scss中使用全局变量 $env 表示 process.env.NODE_ENV
item
  .use('sass-loader')
  .loader('sass-loader')
  .options({
    additionalData: "$env: '" + process.env.NODE_ENV + "';"
  })
  .end();
```

### 修改项目文件
有了 `scss` 可以用的全局变量，有如何在 `scss` 中编写条件语句呢？通过`sass`条件判断修改代码
```html
<style lang="scss" scoped>
$url: $env;
.login {
  @if $url== 'chongqing' {
    background: url('../assets/images/login/lh_login_bg.png') no-repeat;
    background-size: 100% 100%;
    width: 100vw;
    height: 100vh;
  } @else {
    width: 100vw;
    height: 100vh;
    background: url('../assets/images/login/bg.png') no-repeat;
    background-size: 100% 100%;
  }
}
</style>
```

### 结果
这样打包出来的两个项目中，背景图只会出现自己想要的图了，而不会出现其他项目的图。

## 遇到的坑
在 `vue.config.js` 配置文件中配置 `sass-loader` 最先不是用的 `additionalData` ，而是 `data` ，但项目报错，后来谷歌了一下，发现是 `sass-loader` 版本问题，用 `sass-loader` 版本对应的属性即可。
```js
// 可直接在scss中使用全局变量 $env 表示 process.env.NODE_ENV
item
  .use('sass-loader')
  .loader('sass-loader')
  .options({
    additionalData: "$env: '" + process.env.NODE_ENV + "';"
  })
  .end();
```
scss全局引入.env变量
```js
{
  'sass-loader < 7.x' : 'data',
  'sass-loader 8.x' : 'prependData',
  'sass-loader > 9.x' : 'additionalData'
}
```