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
{% note info %}
首先能像element-ui一样简单以组件的方式引入图标，其次能向上图一样列出svg图库，鼠标点击就能够复制该图标使用的代码，如下：
{% endnote %}
``` js
<svg-icon :icon-class="18"/>
```

## svg雪碧图
网上搜寻了一圈，一个简单的解决方案是——`svg雪碧图`。它的`工作原理`是: **利用svg的symbol元素，将每个icon包括在symbol中，通过use元素使用该symbol.**[^1]

这里简单一点的解释就是，最终你的svg icon会变成下面这个样子的 `svg 雪碧图`:
```html
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" id="__SVG_SPRITE_NODE__">
    <symbol class="icon" viewBox="0 0 1024 1024" id="icon名">{{省略的icon path}}</symbol>
    <symbol class="icon" viewBox="0 0 1024 1024" id="icon名">{{省略的icon path}}</symbol>
</svg>
```

你的每一个`icon`都对应着一个`symbol`元素。然后在你的`html`中，`引入`这样的svg, 随后通过`use`在任何你需要icon的地方指向symbol:
```
<use xlink:href="#symbolId"></use>
```
这个过程中，我们可以把symbol理解为sketch中内置的图形，当你需要使用的时候，把这个形状”`拖拽`”到你的`画板`中就行了。而`use`就是这个过程中的”拖拽”行为。

## 准备工作
{% cb 下载svg图片，这里是在阿里图标库中下载的, true %}
{% btn https://www.iconfont.cn/, 阿里图标库 %}
{% cb 安装插件：svg-sprite-loader, true %}
```
npm install --save svg-sprite-loader
```
{% cb 安装插件：v-clipboard, true %}
```
npm install --save v-clipboard
```

## 开始
先从阿里图标库中下载一些svg图片，存放路径为`/src/icons/svg`,然后在 `webpack.base.conf.js` 文件中添加 `rules` 配置[^2]
```js
//webpack.base.conf.js
{
    test: /\.svg$/,
    loader: "svg-sprite-loader",
    include: [path.resolve(__dirname, '../src/icons/svg')],//包括字体图标文件
    // options: {
        //symbolId: 'icon-[name]' //这个没有生效，生效的是默认的name
    // }
}
```
然后修改 `url-loader` 配置
```js
 //webpack.base.conf.js
{
    test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
    loader: "url-loader",
    exclude: [
        path.resolve(__dirname, '../src/icons/svg'), //排除字体图标文件
    ],
    options: {
        limit: 10000,
        name: utils.assetsPath("img/[name].[hash:7].[ext]")
    }
}
```
{% note info %}
这样配置的目的就是为了让`svg-sprite-loader`只处理我们想要处理的svg文件，这些文件存放于指定的文件夹下。
{% endnote %}

创建 vue 组件 svg-icon
```js
<!-- @/components/SvgIcon -->
<template>
    <svg :class="svgClass" aria-hidden="true">
        <use :xlink:href="iconName"></use>
    </svg>
</template>

<script>
/**
 * svg 图标组件
 * iconClass="图标名称"
 * className="风格名称"
 */
export default {
  name: 'svg-icon',
  props: {
    iconClass: { type: String, required: true },
    className: { type: String }
  },
  computed: {
    iconName () {
      return `#${this.iconClass}`
    },
    svgClass () {
      if (this.className) {
        return 'svg-icon ' + this.className
      } else {
        return 'svg-icon'
      }
    }
  }
}
</script>

<style scoped>
.svg-icon {
  width: 20em;
  height: 20em;
  vertical-align: -0.15em;
  fill: currentColor;
  overflow: hidden;
}
</style>
```

在svg文件目录/src/icons里新建index.js文件,文件路径为`/src/icons/index.js`
```js
//index.js
import Vue from 'vue'
import SvgIcon from '@/components/SvgIcon'
/* require.context("./test", false, /.test.js$/);
    这行代码就会去 test 文件夹（不包含子目录） 下面的找所有文件名以 .test.js 结尾的文件能被 require 的文件。
    更直白的说就是 我们可以通过正则匹配引入相应的文件模块。
     require.context有三个参数：
     directory：说明需要检索的目录
     useSubdirectories：是否检索子目录
     regExp: 匹配文件的正则表达式 */
// 全局注册
Vue.component('svg-icon', SvgIcon)
const requireAll = requireContext => requireContext.keys().map(requireContext)
const req = require.context('./svg', false, /\.svg$/)
requireAll(req)
```

在main.js中引入
```js
import './src/icons/index.js'
```

这样就能在vue中使用了 具体格式如下
```html
<svg-icon icon-class="svg文件名"/>
```

## 项目中svg图标快捷使用
到此为止，已经能够实现svg图标当作组件使用了，接下来实现片头动画效果，即点击某个svg图标就得到该图标在项目中使用的代码，这就需要用到插件`v-clipboard`了

在main.js中引入
```js
// 使用clipboard.js进行一键复制文本
import Clipboard from 'v-clipboard';
Vue.use(Clipboard)
```

新建要展示svg图库的页面`SvgChoose.vue`
```js
<template>
    <section class="svg-area">
        <div class="container">
            <el-row :gutter="20">
                <el-col
                    v-clipboard="`<svg-icon :icon-class=&quot;${index + 1}&quot;/>`"
                    v-clipboard:success="clipboardSuccessHandler"
                    v-clipboard:error="clipboardErrorHandler"
                    :span="2"
                    v-for="(item, index) in 20"
                    :key="index"
                    class="single flex-ac flex-pc"
                >
                    <svg-icon :icon-class="index + 1 + ''" />
                </el-col>
            </el-row>
        </div>
    </section>
</template>

<script>
export default {
    data() {
        return {};
    },
    created() {},
    methods: {
        //定义复制失败的回调
        clipboardSuccessHandler({ value, event }) {
            console.log('success', value);
            this.$message.success('代码复制成功，前往粘贴');
        },
        //定义复制成功的回调方法
        clipboardErrorHandler({ value, event }) {
            console.log('error', value);
        },
    },
    components: {},
};
</script>

<style lang="less" scoped>
.svg-area {
    .el-row {
        margin-bottom: 20px;
        &:last-child {
            margin-bottom: 0;
        }
    }
    .el-col {
        border-radius: 4px;
    }
    .bg-purple-dark {
        background: #99a9bf;
    }
    .bg-purple {
        background: #d3dce6;
    }
    .bg-purple-light {
        background: #e5e9f2;
    }
    .grid-content {
        border-radius: 4px;
        min-height: 36px;
    }
    .row-bg {
        padding: 10px 0;
        background-color: #f9fafc;
    }
    .single {
        cursor: pointer;
        height: 40em;
        &:hover {
            background: #f0f0f0;
        }
    }
}
</style>
```
{% note warning %}
上述文件使用需要事先准备一些svg图片，命名从`1.svg`开始递增，项目中还需要引入element-ui达到整体布局和点击弹框。
{% endnote %}

## 参考
[^1]: [懒人神器：svg-sprite-loader实现自己的Icon组件](https://segmentfault.com/a/1190000015367490)
[^2]: [vue 使用svg图片 svg-sprite-loader](https://juejin.cn/post/6844903778244624391)
