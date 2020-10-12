---
uuid: 424c68cc-5640-aa8d-c69f-8e755d1df707
title: vue.use()分析与使用
categories: 技术
tags: 
- vue
---
在使用Vue.js的时候，我们经常会用到vue.use()，比如我们在引入第三方UI库vant时候，会使用到Vue.use(Loading)，或者 Router 和 Vuex 也需要用到，那vue.use()到底是什么？官网给出的解释是: 通过全局方法 Vue.use() 使用插件；接下来分析 一下Vue.use() 的源码和尝试注册一个全局Loading,Toast和Dialog组合方法。
## 前言
官方对 Vue.use() 方法的说明：
- 通过全局方法 `Vue.use()` 使用插件；
-  `Vue.use()` 会自动阻止多次注册相同插件；
- 它需要在你调用  `Vue.use()`  启动应用之前完成；
-  `Vue.use()`  方法至少传入一个参数，该参数类型必须是 Object 或 Function，如果是 Object 那么这个 Object 需要定义一个 install 方法，如果是 Function 那么这个函数就被当做 install 方法。在 `Vue.use()` 执行时 install 会默认执行，当 install 执行时第一个参数就是 Vue，其他参数是 `Vue.use()` 执行时传入的其他参数。

## 源码分析
src/core/global-api/use.js
```js
import { toArray } from '../util/index'
export function initUse (Vue: GlobalAPI) {
  Vue.use = function (plugin: Function | Object) {
    const installedPlugins = (this._installedPlugins || (this._installedPlugins = []))
    if (installedPlugins.indexOf(plugin) > -1) {
      return this
    }
    const args = toArray(arguments, 1)
    args.unshift(this)
    if (typeof plugin.install === 'function') {
      plugin.install.apply(plugin, args)
    } else if (typeof plugin === 'function') {
      plugin.apply(null, args)
    }
    installedPlugins.push(plugin)
    return this
  }
}
```
src/shared/util.js
```js
export function toArray (list: any, start?: number): Array<any> {
  start = start || 0
  let i = list.length - start
  const ret: Array<any> = new Array(i)
  while (i--) {
    ret[i] = list[i + start]
  }
  return ret
}
```
分析

`Vue.use = function (plugin: Function | Object){} `
在全局api Vue 上定义了 use 方法，接收一个 plugin 参数可以是 Function 也可以是 Object，这就和前面官方规定的 Vue.use() 第一个参数要求的类型对应上了。

`if (installedPlugins.indexOf(plugin) > -1) {}`
用来判断该插件是不是已经注册过，防止重复注册。

`const args = toArray(arguments, 1)`
arguments是 Vue.use() 方法的参数列表是一个类数组，后面的 1 先理解成一个常量，toArray 方法的作用就是把第一个 Array 参数从下标为1截取到最后。也就拿到了 Vue.use() 方法除去第一个之外的其他参数，这些参数准备在调用 instll 方法的时候传入。

`if (typeof plugin.install === 'function') {
} else if (typeof plugin === 'function') {}`
这里的if语句是判断 Vue.use() 传入的第一个参数是 Object 还是 Function。

`plugin.install.apply(plugin, args)
plugin.apply(null, args)`
判断完之后执行那个对应的 install 方法，用 apply 改变 this 指向，并把 toArray 得到的剩余参数传入。

`installedPlugins.push(plugin)`
最后记录该组件已经注册过了.

现在我们发现 Vue.use() 的注册`本质上就是执行了一个 install 方法`，install 里的内容由开发者自己定义，通俗讲就是一个钩子可能更贴近语义化而已。
## Vue.use()有什么用
官方文档是这么描述的：插件通常用来为 Vue 添加全局功能。插件的功能范围没有严格的限制——一般有下面几种：
```js
MyPlugin.install = function (Vue, options) {
  // 1. 添加全局方法或 property
  Vue.myGlobalMethod = function () {
    // 逻辑...
  }

  // 2. 添加全局资源
  Vue.directive('my-directive', {
    bind (el, binding, vnode, oldVnode) {
      // 逻辑...
    }
    ...
  })

  // 3. 注入组件选项
  Vue.mixin({
    created: function () {
      // 逻辑...
    }
    ...
  })

  // 4. 添加实例方法
  Vue.prototype.$myMethod = function (methodOptions) {
    // 逻辑...
  }
}
```
在 install 里我们可以拿到 Vue 那么和 Vue 相关的周边工作都可以考虑放在 Vue.use() 方法里，接下来我们使用一下Vue.use来开发一个自定义Loading,Toast和Dialog组合方法。
## 注册一个全局Loading,Toast和Dialog组合方法
/common/components/Loading.vue
```js
<template>
    <div class="dialog" v-if="show">
        <div class="loading" v-if="type == 1">
            <div class="loading-container">
                <div class="loading-box"></div>
                <p class="loading-text">{{dialogStr}}</p>
            </div>
        </div>
        <div class="toast" v-if="type == 2">
            <div class="toast-box">
                <p>{{dialogStr}}</p>
            </div>
        </div>
        <div class="alert-dialog" @touchmove.prevent v-if="type == 3">
            <div class="dialog-bg"></div>
            <div class="alert-container">
                <div class="alert-title" v-if="dialogTitle">{{dialogTitle}}</div>
                <div class="alert-text">{{dialogStr}}</div>
                <div class="alert-btns">
                    <button type="button" @click="show=false;cancelFn()" v-if="cancelBtnStr">{{cancelBtnStr}}</button>
                    <button type="button" @click="show=false;okFn()" v-if="okBtnStr">{{okBtnStr}}</button>
                </div>
            </div>
        </div>
    </div>
</template>
<style lang="scss" scoped>
.dialog {
    position: fixed;
    top:0;
    left:0;
    width:100%;
    height:100%;
    background-color: rgba(0, 0, 0, 0);
    z-index: 100;
}
.loading {
    width: 100%;
    height: 100%;
    .loading-container {
        min-height: 0.8rem;
        min-width: 0.8rem;
        padding: 0.1rem;
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);
        background: rgba(18, 18, 18, 0.7);
        color: #fff;
        border-radius: 5px;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        z-index: 999;
        .loading-box {
            background: transparent
                url("data:image/svg+xml;charset=utf8, %3Csvg xmlns='http://www.w3.org/2000/svg' width='120' height='120' viewBox='0 0 100 100'%3E%3Cpath fill='none' d='M0 0h100v100H0z'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='%23E9E9E9' rx='5' ry='5' transform='translate(0 -30)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='%23989697' rx='5' ry='5' transform='rotate(30 105.98 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='%239B999A' rx='5' ry='5' transform='rotate(60 75.98 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='%23A3A1A2' rx='5' ry='5' transform='rotate(90 65 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='%23ABA9AA' rx='5' ry='5' transform='rotate(120 58.66 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='%23B2B2B2' rx='5' ry='5' transform='rotate(150 54.02 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='%23BAB8B9' rx='5' ry='5' transform='rotate(180 50 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='%23C2C0C1' rx='5' ry='5' transform='rotate(-150 45.98 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='%23CBCBCB' rx='5' ry='5' transform='rotate(-120 41.34 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='%23D2D2D2' rx='5' ry='5' transform='rotate(-90 35 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='%23DADADA' rx='5' ry='5' transform='rotate(-60 24.02 65)'/%3E%3Crect width='7' height='20' x='46.5' y='40' fill='%23E2E2E2' rx='5' ry='5' transform='rotate(-30 -5.98 65)'/%3E%3C/svg%3E")
                no-repeat;
            margin: 0 auto;
            width: 0.4rem;
            height: 0.4rem;
            animation: e 1s steps(12) infinite;
            background-size: contain;
        }
        .loading-text {
            font-size: 0.14rem;
            text-align: center;
        }
    }
}
.toast {
    position: absolute;
    bottom: 0.6rem;
    font-size: 0.16rem;
    left: 50%;
    transform: translate(-50%);
    z-index: 999;
    .toast-box {
        padding: 0.1rem;
        background-color: rgba(0, 0, 0, 0.6);
        border-radius: 0.05rem;
        word-break: break-word;
        color: #fff;
    }
}
.alert-dialog {
    width: 100%;
    height: 100%;
    .dialog-bg {
        position: absolute;
        width: 100%;
        height: 100%;
        background-color: rgba(0, 0, 0, 0.6);
    }
    .alert-container {
        position: absolute;
        min-width: 70%;
        min-height: 0.5rem;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);
        background-color: #fff;
        z-index: 999;
        border-radius: 5px;
        overflow: hidden;
        .alert-title {
            font-size: 0.15rem;
            text-align: left;
            font-weight: 400;
            padding: 0.1rem 0.15rem 0;
        }
        .alert-text {
            font-size: 0.14rem;
            text-align: left;
            padding: 0.2rem 0.05rem 0.2rem 0.2rem;
            max-height: 3rem;
            overflow: auto;
            color: #999;
        }
        .alert-btns {
            width: 100%;
            display: flex;
            border-top: 1px solid #d5d5d6;
            button {
                flex: 1;
                border: none;
                height: 0.4rem;
                font-size: 0.14rem;
                background-color: #fff;
                &:first-child {
                    border-right: 1px solid #d5d5d6;
                }
                &:nth-child(2) {
                    color: #2aa4f1;
                }
            }
        }
    }
}
@keyframes e {
    0% {
        transform: rotate(0deg);
    }
    to {
        transform: rotate(1turn);
    }
}
</style>
```
/common/utils/loading.js
```js
/*
 * @Description: 动态的loading和toast和dialog弹框组件
 */
import Dialog from '../components/common/Dialog.vue';

/**
 * @param {注册toast组件} Vue对象
 */
function registryToast(Vue) {
    // 将组件注册到 vue 的 原型链里去,
    // 这样就可以在所有 vue 的实例里面使用 this.$toast()
    Vue.prototype.$toast = showToast;
    Vue.prototype.$loading = showLoading;
    Vue.prototype.$dialog = showDialog;
    const DialogConstructor = Vue.extend(Dialog);
    let loadingDom = null;
    let dialogDom = null; //弹框调用状态
    /**
     * loading框显示或隐藏
     * @param {状态位} flag 
     * @param {提示文本} dialogStr 
     */
    function showLoading(flag, dialogStr) {
        if (!loadingDom) {
            // 实例化一个 toast.vue
            loadingDom = new DialogConstructor({
                el: document.createElement('div'),
                data() {
                    return {
                        type: 1,
                        show: flag,
                        dialogStr: dialogStr || '加载中'
                    };
                }
            });
            // 把 实例化的 toast.vue 添加到 body 里
            document.body.appendChild(loadingDom.$el);
        }
        loadingDom.show = flag;
    }
    /**
     * 显示toast弹框
     * @param {toast弹框文本串} dialogStr 
     */
    function showToast(dialogStr, time) {
        // 实例化一个 toast.vue
        const toastDom = new DialogConstructor({
            el: document.createElement('div'),
            data() {
                return {
                    dialogStr: dialogStr,
                    type: 2,
                    show: true
                };
            }
        });
        // 把 实例化的 toast.vue 添加到 body 里
        document.body.appendChild(toastDom.$el);
        // 过了 duration 时间后隐藏
        let timer = setTimeout(() => {
            toastDom.show = false;
            clearTimeout(timer);
        }, time || 1800);
    }
    /**
     * 基础的dialog框
     * @param {弹框参数} options 
     */
    function showDialog(options) {
        let defaultOptions = {
            show: true, //是否显示
            type: 3, //类型
            dialogStr: '提示内容', //弹框内容
            dialogTitle: '', //标题
            okBtnStr: '确定', //确定按钮文本
            cancelBtnStr: '取消', //取消按钮文本
            okFn: () => { //确定点击回调事件
                console.log('你点击了确定按钮');
            },
            cancelFn: () => { //取消点击回调事件
                console.log('你点击了取消按钮');
            }
        };
        Object.assign(defaultOptions, options);
        if (!dialogDom) {
            dialogDom = new DialogConstructor({
                el: document.createElement('div'),
                data() {
                    return defaultOptions;
                }
            });
            document.body.appendChild(dialogDom.$el);
        }else{
            Object.assign(dialogDom, options);
        }
        dialogDom.show = true;
    }
}
export default registryToast;
```