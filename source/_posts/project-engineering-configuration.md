---
title: 前端项目工程环境规范化
author: Revan
index_img: /img/engineering.jpg
category: 技术
tags:
  - Eslint
  - Prettier
  - Vite
  - Vue3.x
  - TypeScript
excerpt: “前端工程化”这个概念已经越来越流行了，其中涉及到的“代码规范”，“提交规范”和“自动部署”是非常必要的，有助于团队开发，本文从 0 开始搭建一套规范的 Vite + Vue3 + TypeScript 前端工程化项目环境。
date: 2021-05-12 10:05:48
---

{% note info %}
`Vue3` 跟 `Vite` 正式版发布有很长一段时间了，生态圈也渐渐丰富起来，作者已在多个项目中使用，总结一下：就是快！也不用担心稳定性问题，开发体验真不是一般好！还没尝试的同学可以从本文开始学习，从 0 开始手把手带你搭建一套规范的 `Vite` + `Vue3` + `TypeScript` 前端工程化项目环境。
{% endnote %}
## 引言
本文将从一下方面展开：
- 架构搭建
- 代码规范
- 提交规范
- 自动部署

## 架构搭建

### 准备工作
请确保你的电脑上成功安装 `Node.js`，本项目使用 `Vite` 构建工具，需要 `Node.js` 版本 `>= 12.0.0`。

查看 `Node.js` 版本：
```js
node -v   // v14.16.1
```

建议将 `Node.js` 升级到最新的稳定版本：
```js
// 使用 nvm 安装最新稳定版 Node.js
nvm install stable
```

`npm` 换源（使用淘宝镜像）
```js
// 持久使用
npm config set registry https://registry.npm.taobao.org

// 查看npm源地址
npm config get registry   // https://registry.npm.taobao.org/
```

### 使用 Vite 快速初始化项目雏形
使用 `NPM`:
```js
npm init @vitejs/app
```
然后按照终端提示完成以下操作：
1. 输入项目名称: `vite-vue3-tester`
2. 选择框架：`vue`
3. 选择语言类型：`Typescript`
4. 进入项目：
   
```js
cd vite-vue3-tester
```
5. 安装依赖:
   
```js
npm install
```
6. 启动项目:
   
```js
npm run dev
```

项目成功运行表示 `Vite + Vue3 + TypeScript` 简单的项目骨架搭建完毕，下面我们来为这个项目集成 `Vue Router`、`Vuex`、`Element Plus`、`Axios`、`Stylus/Sass/Less`。

### 修改 Vite 配置文件
`Vite` 配置文件 `vite.config.ts` 位于根目录下，项目启动时会自动读取。

本项目先做一些简单配置，例如：设置 `@` 指向 `src` 目录、 服务启动端口、打包路径、代理等。
```ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
// 如果编辑器提示 path 模块找不到，则可以安装一下 @types/node -> npm i @types/node -D
import { resolve } from 'path'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue()],
  resolve: {
    alias: {
      '@': resolve(__dirname, 'src') // 设置 `@` 指向 `src` 目录
    }
  },
  base: './', // 设置打包路径
  server: {
    port: 4000, // 设置服务启动端口号
    open: true, // 设置服务启动时是否自动打开浏览器
    cors: true // 允许跨域

    // 设置代理，根据我们项目实际情况配置
    // proxy: {
    //   '/api': {
    //     target: 'http://xxx.xxx.xxx.xxx:8000',
    //     changeOrigin: true,
    //     secure: false,
    //     rewrite: (path) => path.replace('/api/', '/')
    //   }
    // }
  }
})
```
如果编辑器提示 `path` 模块找不到，则可以安装一下 `@types/node` -> `npm i @types/node -D`

### 规范目录结构
```js
├── publish/
└── src/
    ├── assets/                    // 静态资源目录
    ├── common/                    // 通用类库目录
    ├── components/                // 公共组件目录
    ├── router/                    // 路由配置目录
    ├── store/                     // 状态管理目录
    ├── style/                     // 通用 CSS 目录
    ├── utils/                     // 工具函数目录
    ├── views/                     // 页面组件目录
    ├── App.vue
    ├── main.ts
    ├── shims-vue.d.ts
├── tests/                         // 单元测试目录
├── index.html
├── tsconfig.json                  // TypeScript 配置文件
├── vite.config.ts                 // Vite 配置文件
└── package.json
```

### 集成路由工具 Vue Router
1. 安装支持 Vue3 的路由工具 vue-router@4

```js
npm i vue-router@4
```
2. 创建 `src/router/index.ts` 文件

```js
import {
  createRouter,
  createWebHashHistory,
  RouteRecordRaw
} from 'vue-router'
import Home from '@/views/home.vue'
import Vuex from '@/views/vuex.vue'

const routes: Array<RouteRecordRaw> = [
  {
    path: '/',
    name: 'Home',
    component: Home
  },
  {
    path: '/vuex',
    name: 'Vuex',
    component: Vuex
  },
  {
    path: '/axios',
    name: 'Axios',
    component: () => import('@/views/axios.vue') // 懒加载组件
  }
]

const router = createRouter({
  history: createWebHashHistory(),
  routes
})

export default router
```
根据本项目路由配置的实际情况，你需要在 `src` 下创建 `views` 目录，用来存储页面组件。

我们在 `views` 目录下创建 `home.vue` 、`vuex.vue` 、`axios.vue`。

例如，`src/views/home.vue`：
```js
<template>
  <div>home</div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'

export default defineComponent({
  name: 'Home'
})
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```
3. 在 `main.ts` 文件中挂载路由配置

```js
import { createApp } from 'vue'
import App from './App.vue'

import router from './router/index'

createApp(App).use(router).mount('#app')
```
4. 修改`src/App.vue` => `router-view`

```js
<template>
  <router-view></router-view>
</template>

<script lang="ts">
import { defineComponent } from 'vue'

export default defineComponent({
  name: 'App'
})
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```
这样，`路由` 就配置好了。

### 集成状态管理工具 Vuex
1. 安装支持 `Vue3` 的状态管理工具 `vuex@next`

```js
npm i vuex@next
```
2. 创建 `src/store/index.ts` 文件

```js
import { createStore } from 'vuex'

const defaultState = {
  count: 0
}

// Create a new store instance.
export default createStore({
  state() {
    return defaultState
  },
  mutations: {
    increment(state: typeof defaultState) {
      state.count++
    }
  },
  actions: {
    increment(context) {
      context.commit('increment')
    }
  },
  getters: {
    double(state: typeof defaultState) {
      return 2 * state.count
    }
  }
})
```

3. 在 `main.ts` 文件中挂载 `Vuex` 配置

```js
import { createApp } from 'vue'
import App from './App.vue'
import router from './router/index'
import store from './store/index'

createApp(App).use(router).use(store).mount('#app')
```

4. 测试，在`src/views/home.vue`中加上：

```html
<button @click="$store.commit('increment')">{{$store.state.count}}</button>
```

### 集成 UI 框架 Element Plus
1. 安装支持 `Vue3` 的 `UI` 框架 `Element Plus`

```js
npm i element-plus
```
2. 在 `main.ts` 文件中挂载 `Element Plus`

```js
import { createApp } from 'vue'
import App from './App.vue'
import router from './router/index'
import store from './store/index'
import ElementPlus from 'element-plus'
import 'element-plus/lib/theme-chalk/index.css'

createApp(App).use(router).use(store).use(ElementPlus).mount('#app')
```

3. 测试，在`src/views/home.vue`中改成：

```html
<el-button type="primary" @click="$store.commit('increment')">{{$store.state.count}}</el-button>
```
### 集成 HTTP 工具 Axios
1. 安装 `Axios`（`Axios` 跟 `Vue` 版本没有直接关系，安装最新即可）

```js
npm i axios
```
2. 配置 `Axios`，新建文件`src/utils/axios.ts`

```js
import Axios from 'axios'
import { ElMessage } from 'element-plus'

const baseURL = 'https://api.github.com'

const axios = Axios.create({
  baseURL,
  timeout: 20000 // 请求超时 20s
})

// 前置拦截器（发起请求之前的拦截）
axios.interceptors.request.use(
  (response) => {
    /**
     * 根据你的项目实际情况来对 config 做处理
     * 这里对 config 不做任何处理，直接返回
     */
    return response
  },
  (error) => {
    return Promise.reject(error)
  }
)

// 后置拦截器（获取到响应时的拦截）
axios.interceptors.response.use(
  (response) => {
    /**
     * 根据你的项目实际情况来对 response 和 error 做处理
     * 这里对 response 和 error 不做任何处理，直接返回
     */
    return response
  },
  (error) => {
    if (error.response && error.response.data) {
      const code = error.response.status
      const msg = error.response.data.message
      ElMessage.error(`Code: ${code}, Message: ${msg}`)
      console.error(`[Axios Error]`, error.response)
    } else {
      ElMessage.error(`${error}`)
    }
    return Promise.reject(error)
  }
)

export default axios
```
3. 测试，在`src/views/axios.vue`中引入 `Axios` 配置文件:

```js
<template></template>
<script lang="ts">
  import { defineComponent } from 'vue'
  import axios from '../utils/axios'

  export default defineComponent({
    setup() {
      axios
        .get('/users/XPoet')
        .then((res) => {
          console.log('res: ', res)
        })
        .catch((err) => {
          console.log('err: ', err)
        })
    }
  })
</script>
```

### 集成 CSS 预编译器 Stylus/Sass/Less
这里以`Less`为例：
1. 安装

```js
npm i less -D
```
2. 使用，在`src/views/home.vue`中：

```css
<style lang="less">
@bg : #f0f0f0;
.home-area{
  background: @bg;
}
</style>
```
至此，一个基于 `TypeScript + Vite + Vue3 + Vue Router + Vuex + Element Plus + Axios + Stylus/Sass/Less` 的前端项目开发环境搭建完毕。

## 代码规范
> 随着前端应用逐渐变得大型化和复杂化，在同一个项目中有多个人员参与时，每个人的前端能力程度不等，他们往往会用不同的编码风格和习惯在项目中写代码，长此下去，势必会让项目的健壮性越来越差。解决这些问题，理论上讲，口头约定和代码审查都可以，但是这种方式无法实时反馈，而且沟通成本过高，不够灵活，更关键的是无法把控。不以规矩，不能成方圆，我们不得不在项目使用一些工具来约束代码规范。

本章讲解如何使用 `EditorConfig` + `Prettier` + `ESLint` 组合来实现代码规范化。

这样做带来好处：

- 解决团队之间代码不规范导致的`可读性差`和`可维护性差`的问题。
- 解决团队成员`不同编辑器`导致的编码规范不统一问题。
- 提前发现`代码风格`问题，给出对应规范提示，及时修复。
- `减少代码审查`过程中反反复复的修改过程，节约时间。
- `自动格式化`，统一编码风格，从此和脏乱差的代码说再见。

### 集成 EditorConfig 配置
`EditorConfig` 有助于为不同 `IDE` 编辑器上处理同一项目的多个开发人员维护一致的编码风格。

在项目根目录下增加 `.editorconfig` 文件：
```js
# Editor configuration, see http://editorconfig.org

# 表示是最顶层的 EditorConfig 配置文件
root = true

[*] # 表示所有文件适用
charset = utf-8 # 设置文件字符集为 utf-8
indent_style = space # 缩进风格（tab | space）
indent_size = 4 # 缩进大小
end_of_line = lf # 控制换行类型(lf | cr | crlf)
trim_trailing_whitespace = true # 去除行首的任意空白字符
insert_final_newline = true # 始终在文件末尾插入一个新行

[*.md] # 表示仅 md 文件适用以下规则
max_line_length = off
trim_trailing_whitespace = false
```
注意：
`VSCode` 使用 `EditorConfig` 需要去插件市场下载插件 `EditorConfig for VS Code` 。

`JetBrains` 系列（`WebStorm`、`IntelliJ IDEA` 等）则不用额外安装插件，可直接使用 `EditorConfig` 配置。

### 集成 Prettier 配置
`Prettier` 是一款强大的代码格式化工具，支持 `JavaScript、TypeScript、CSS、SCSS、Less、JSX、Angular、Vue、GraphQL、JSON、Markdown` 等语言，基本上前端能用到的文件格式它都可以搞定，是当下最流行的代码格式化工具。
1. 安装 `Prettier`

```js
npm i prettier -D
```
2. 在本项目根目录下创建 `.prettierrc` 文件。

```json
{
  "useTabs": false,
  "tabWidth": 4,
  "printWidth": 100,
  "singleQuote": true,
  "trailingComma": "none",
  "bracketSpacing": true,
  "semi": true
}
```

3. `Prettier` 安装且配置好之后，就能使用命令来格式化代码

```js
# 格式化所有文件（. 表示所有文件）
npx prettier --write .
```
这样，执行完命令之后，代码都格式化成统一样式了。

注意：
`VSCode` 编辑器使用 `Prettier` 配置需要下载插件 `Prettier - Code formatter` 。
`JetBrains` 系列编辑器（`WebStorm`、`IntelliJ IDEA` 等）则不用额外安装插件，可直接使用 `Prettier` 配置。

`Prettier` 配置好以后，在使用 `VSCode` 或 `WebStorm` 等编辑器的格式化功能时，编辑器就会按照 `Prettier` 配置文件的规则来进行格式化，避免了因为大家编辑器配置不一样而导致格式化后的代码风格不统一的问题。

### 集成 ESLint 配置
`ESLint` 是一款用于查找并报告代码中问题的工具，并且支持部分问题自动修复。其核心是通过对代码解析得到的 `AST`（Abstract Syntax Tree 抽象语法树）进行模式匹配，来分析代码达到检查代码质量和风格问题的能力。

正如前面我们提到的因团队成员之间编程能力和编码习惯不同所造成的代码质量问题，我们使用 `ESLint` 来解决，`一边写代码一边查找问题`，如果发现错误，就给出`规则提示`，并且`自动修复`，长期下去，可以促使团队成员往同一种编码风格靠拢。

1. 安装 ESLint

```js
npm i eslint -D
```

2. 配置 ESLint
ESLint 安装成功后，执行 `npx eslint --init`，然后按照终端操作提示完成一系列设置来创建配置文件。
依次选择：
- How would you like to use ESLint? （你想如何使用 ESLint?）：**To check syntax, find problems, and enforce code style（检查语法、发现问题并强制执行代码风格）**
- What type of modules does your project use?（你的项目使用哪种类型的模块?）：**JavaScript modules (import/export)**
- Which framework does your project use? （你的项目使用哪种框架?）：**Vue.js**
- Does your project use TypeScript?（你的项目是否使用 TypeScript？）：**Yes**
- Where does your code run?（你的代码在哪里运行?）：**选择 Browser 和 Node**
- How would you like to define a style for your project?（你想怎样为你的项目定义风格？）：**Use a popular style guide（使用一种流行的风格指南）**
- Which style guide do you want to follow?（你想遵循哪一种风格指南?）：**Airbnb**
- What format do you want your config file to be in?（你希望你的配置文件是什么格式?）：**JavaScript**
- Would you like to install them now with npm?（你想现在就用 NPM 安装它们吗?）：**Yes**

注意：如果自动安装依赖失败，那么需要手动安装
```js
npm i @typescript-eslint/eslint-plugin @typescript-eslint/parser eslint-config-airbnb-base eslint-plugin-import eslint-plugin-vue -D
```

3. ESLint 配置文件 .eslintrc.js

在上一步操作完成后，会在项目根目录下自动生成 `.eslintrc.js` 配置文件：
```js
module.exports = {
  env: {
    browser: true,
    es2021: true,
    node: true,
  },
  extends: [
    'plugin:vue/essential',
    'airbnb-base',
  ],
  parserOptions: {
    ecmaVersion: 12,
    parser: '@typescript-eslint/parser',
    sourceType: 'module',
  },
  plugins: [
    'vue',
    '@typescript-eslint',
  ],
  rules: {
  },
};
```
注意：
`VSCode` 使用 `ESLint` 配置文件需要去插件市场下载插件 `ESLint` 。

`JetBrains` 系列（`WebStorm`、`IntelliJ IDEA` 等）则不用额外安装插件。

配置好以后，我们在 `VSCode` 或 `WebStorm` 等编辑器中开启 `ESLin`(`VSCode`编辑器右下角点击`ESLint`选择`allow`)，写代码时，`ESLint` 就会按照我们配置的规则来进行实时代码检查，发现问题会给出对应错误提示和修复方案。

这样，我们发现我们项目中的很多文件都标红了，表示这些代码不符合`ESLint`规范。

虽然，现在编辑器已经给出错误提示和修复方案，但需要我们一个一个去点击修复，还是挺麻烦的。很简单，我们只需设置编辑器保存文件时自动执行 `eslint --fix` 命令进行代码风格修复。

`VSCode` 在 `settings.json` 设置文件中，增加以下代码：
```js
"editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
}
```
这时，我们去标红的文件中保存文件，`ctrl+s`，格式就被自动修改成`ESLint`规范了。

但是依然有一些文件显示不符合`ESLint`规范，修改`.eslintrc.js`：
```js
module.exports = {
    env: {
        browser: true,
        es2021: true,
        node: true,
    },
    extends: [
        'plugin:vue/essential',
        'airbnb-base',
    ],
    parserOptions: {
        ecmaVersion: 12,
        parser: '@typescript-eslint/parser',
        sourceType: 'module',
    },
    plugins: [
        'vue',
        '@typescript-eslint',
    ],
    settings: {
        'import/resolver': {
            node: {
                extensions: ['.js', '.jsx', '.ts', '.tsx'],
            },
        },
    },
    rules: {
        'linebreak-style': 0,
        indent: ['error', 4], // eslint缩进四格
        'import/extensions': [2, 'never', { 'web.js': 'never', json: 'never' }],
        'import/no-extraneous-dependencies': [2, {
            devDependencies: true,
            peerDependencies: true,
            // optionalDependencies: true,
            // bundledDependencies: true
        }],
        'import/no-unresolved': [
            2,
            { caseSensitive: false },
        ],
    },
};
```

### 解决 Prettier 和 ESLint 的冲突
通常大家会在项目中根据实际情况添加一些额外的 ESLint 和 Prettier 配置规则，难免会存在规则冲突情况。

解决两者冲突问题，需要用到 `eslint-plugin-prettier` 和 `eslint-config-prettier`。

- `eslint-plugin-prettier` 将 `Prettier` 的规则设置到 ESLint 的规则中。
- `eslint-config-prettier` 关闭 `ESLint` 中与 `Prettier` 中会发生冲突的规则。

最后形成优先级：`Prettier` 配置规则 > `ESLint` 配置规则。

安装插件
```js
npm i eslint-plugin-prettier eslint-config-prettier -D
```

在 `.eslintrc.js` 添加 `prettier` 插件
```js
module.exports = {
  ...
  extends: [
    'plugin:vue/essential',
    'airbnb-base',
    'plugin:prettier/recommended' // 添加 prettier 插件
  ],
  ...
}
```
这样，我们在执行 `eslint --fix` 命令时，`ESLint` 就会按照 `Prettier` 的配置规则来格式化代码，轻松解决二者冲突问题。

### 集成 husky 和 lint-staged
我们在项目中已集成 `ESLint` 和 `Prettier`，在编码时，这些工具可以对我们写的代码进行实时校验，在一定程度上能有效规范我们写的代码，但团队可能会有些人觉得这些条条框框的限制很麻烦，选择视“提示”而不见，依旧按自己的一套风格来写代码，或者干脆禁用掉这些工具，开发完成就直接把代码提交到了仓库，日积月累，`ESLint` 也就形同虚设。

所以，我们还需要做一些限制，让没通过 `ESLint` 检测和修复的代码`禁止提交`，从而保证仓库代码都是符合规范的。

为了解决这个问题，我们需要用到 `Git Hook`，在本地执行 `git commit` 的时候，就对所提交的代码进行 `ESLint` 检测和修复（即执行 `eslint --fix`），如果这些代码没通过 `ESLint` 规则校验，则禁止提交。

**配置 husky**
使用 `husky-init` 命令快速在项目初始化一个 `husky` 配置。
```js
npx husky-init && npm install
```
**注意**，这里项目前提有git，不然无法运行。这里我们把当前项目部署到`github`中，后面也会用到`github`，部署方法略。

这行命令做了四件事：
1. 安装 `husky` 到开发依赖
2. 在项目根目录下创建 `.husky` 目录
3. 在 `.husky` 目录创建 `pre-commit hook`，并初始化 `pre-commit` 命令为` npm test`
4. 修改 `package.json` 的 `scripts`，增加 `"prepare": "husky install"`

到这里，`husky` 配置完毕，现在我们来使用它：
修改 `.husky/pre-commit` hook 文件的触发命令：
```js
eslint --fix ./src --ext .vue,.js,.ts
```

上面这个 `pre-commit` hook 文件的作用是：当我们执行 `git commit -m "xxx"` 时，会先对 `src` 目录下所有的 `.vue`、`.js`、`.ts`  文件执行 `eslint --fix` 命令，如果 `ESLint` 通过，成功 `commit`，否则终止 `commit`。

但是又存在一个问题：有时候我们明明只改动了一两个文件，却要对所有的文件执行 `eslint --fix`。假如这是一个历史项目，我们在中途配置了 `ESLint` 规则，那么在提交代码时，也会对其他未修改的“历史”文件都进行检查，可能会造成大量文件出现 `ESLint` 错误，显然不是我们想要的结果。

我们要做到只用 `ESLint` 修复自己此次写的代码，而不去影响其他的代码。所以我们还需借助一个神奇的工具 `lint-staged` 。

`lint-staged` 这个工具一般结合 `husky` 来使用，它可以让 `husky` 的 `hook` 触发的命令只作用于 `git add`那些文件（即 `git` 暂存区的文件），而不会影响到其他文件。

接下来，我们使用 `lint-staged` 继续优化项目。

1. 安装 `lint-staged`

```js
npm i lint-staged -D
```
2. 在 `package.json`里增加 `lint-staged` 配置项

```js
"lint-staged": {
  "*.{vue,js,ts}": "eslint --fix"
},
```
这行命令表示：只对 `git` 暂存区的 `.vue`、`.js`、`.ts` 文件执行 `eslint --fix`。

3. 修改 `.husky/pre-commit` hook 的触发命令为：`npx lint-staged`

至此，`husky` 和 `lint-staged` 组合配置完成。

现在我们提交代码时就会变成这样：

假如我们修改了 `scr` 目录下的 `test-1.js`文件，然后 `git add ./src/`，最后 `git commit -m "test..."`，这时候就会只对 `test-1.js` 这两个文件执行 1。如果 ESLint 通过，成功提交，否则自动修复代码格式再提交。从而保证了我们提交到 `Git` 仓库的代码都是规范的。

无论写代码还是做其他事情，都应该用长远的眼光来看，刚开始使用 `ESint` 的时候可能会有很多问题，改起来也很费时费力，只要坚持下去，代码质量和开发效率都会得到提升，前期的付出都是值得的。

这些工具并不是必须的，没有它们你同样可以可以完成功能开发，但是利用好这些工具，你可以写出更高质量的代码。特别是一些刚刚接触的人，可能会觉得麻烦而放弃使用这些工具，失去了一次提升编程能力的好机会。

## 提交规范
前面我们已经统一代码规范，并且在提交代码时进行强约束来保证仓库代码质量。多人协作的项目中，在提交代码这个环节，也存在一种情况：`git commit` 不能保证每个人对提交信息的准确描述，因此会出现提交信息紊乱、风格不一致的情况。

如果 `git commit` 的描述信息精准，在后期维护和 `Bug` 处理时会变得有据可查，项目开发周期内还可以根据规范的提交信息快速生成开发日志，从而方便我们追踪项目和把控进度。

这里，我们使用社区最流行、最知名、最受认可的 `Angular` 团队提交规范。

### 集成 Commitizen 实现规范提交
上面提到的 `Angular` 规范提交的格式，初次接触的同学咋一看可能会觉得复杂，其实不然，如果让大家在 `git commit` 的时候严格按照上面的格式来写，肯定是有压力的，首先得记住不同的类型到底是用来定义什么，`subject` 怎么写，`body` 怎么写，`footer` 要不要写等等问题，`懒才是程序员第一生产力`，为此我们使用 `Commitizen` 工具来帮助我们自动生成 `commit message` 格式，从而实现规范提交。

> Commitizen 是一个帮助撰写规范 commit message 的工具。它有一个命令行工具 cz-cli。


1. 安装 `Commitizen`

```js
npm install commitizen -D
```
2. 初始化项目

成功安装 `Commitizen` 后，我们用 `cz-conventional-changelog` 适配器来初始化项目：
```js
npx commitizen init cz-conventional-changelog --save-dev --save-exact
```
3. 使用 `Commitizen`

以前我们提交代码都是 `git commit -m "xxx"`，现在改为 `git cz`，然后按照终端操作提示，逐步填入信息，就能自动生成规范的 `commit message`。

最后，在 Git 提交历史中就能看到刚刚规范的提交记录了。

4. 自定义配置提交说明

从上面的截图可以看到，`git cz` 终端操作提示都是`英文`的，如果想改成`中文`的或者自定义这些配置选项，我们使用 `cz-customizable` 适配器。

`cz-customizable` 初始化项目

运行如下命令使用 `cz-customizable` 初始化项目，注意之前已经初始化过一次，这次再初始化，需要加 `--force` 覆盖。

```js
npx commitizen init cz-customizable --save-dev --save-exact --force
```
5. 使用 cz-customizable

在项目根目录下创建 `.cz-config.js` 文件，然后按照官方提供的示例来配置。

在本项目中我们修改成中文：
```js
module.exports = {
  // type 类型（定义之后，可通过上下键选择）
  types: [
    { value: 'feat', name: 'feat:     新增功能' },
    { value: 'fix', name: 'fix:      修复 bug' },
    { value: 'docs', name: 'docs:     文档变更' },
    { value: 'style', name: 'style:    代码格式（不影响功能，例如空格、分号等格式修正）' },
    { value: 'refactor', name: 'refactor: 代码重构（不包括 bug 修复、功能新增）' },
    { value: 'perf', name: 'perf:     性能优化' },
    { value: 'test', name: 'test:     添加、修改测试用例' },
    { value: 'build', name: 'build:    构建流程、外部依赖变更（如升级 npm 包、修改 webpack 配置等）' },
    { value: 'ci', name: 'ci:       修改 CI 配置、脚本' },
    { value: 'chore', name: 'chore:    对构建过程或辅助工具和库的更改（不影响源文件、测试用例）' },
    { value: 'revert', name: 'revert:   回滚 commit' }
  ],

  // scope 类型（定义之后，可通过上下键选择）
  scopes: [
    ['components', '组件相关'],
    ['hooks', 'hook 相关'],
    ['utils', 'utils 相关'],
    ['element-ui', '对 element-ui 的调整'],
    ['styles', '样式相关'],
    ['deps', '项目依赖'],
    ['auth', '对 auth 修改'],
    ['other', '其他修改'],
    // 如果选择 custom，后面会让你再输入一个自定义的 scope。也可以不设置此项，把后面的 allowCustomScopes 设置为 true
    ['custom', '以上都不是？我要自定义']
  ].map(([value, description]) => {
    return {
      value,
      name: `${value.padEnd(30)} (${description})`
    }
  }),

  // 是否允许自定义填写 scope，在 scope 选择的时候，会有 empty 和 custom 可以选择。
  // allowCustomScopes: true,

  // allowTicketNumber: false,
  // isTicketNumberRequired: false,
  // ticketNumberPrefix: 'TICKET-',
  // ticketNumberRegExp: '\\d{1,5}',


  // 针对每一个 type 去定义对应的 scopes，例如 fix
  /*
  scopeOverrides: {
    fix: [
      { name: 'merge' },
      { name: 'style' },
      { name: 'e2eTest' },
      { name: 'unitTest' }
    ]
  },
  */

  // 交互提示信息
  messages: {
    type: '确保本次提交遵循 Angular 规范！\n选择你要提交的类型：',
    scope: '\n选择一个 scope（可选）：',
    // 选择 scope: custom 时会出下面的提示
    customScope: '请输入自定义的 scope：',
    subject: '填写简短精炼的变更描述：\n',
    body:
      '填写更加详细的变更描述（可选）。使用 "|" 换行：\n',
    breaking: '列举非兼容性重大的变更（可选）：\n',
    footer: '列举出所有变更的 ISSUES CLOSED（可选）。 例如: #31, #34：\n',
    confirmCommit: '确认提交？'
  },

  // 设置只有 type 选择了 feat 或 fix，才询问 breaking message
  allowBreakingChanges: ['feat', 'fix'],

  // 跳过要询问的步骤
  skipQuestions: ['body', 'footer'],

  // subject 限制长度
  subjectLimit: 100,
  breaklineChar: '|', // 支持 body 和 footer
  // footerPrefix : 'ISSUES CLOSED:'
  // askForBreakingChangeFirst : true,
}
```
建议大家结合项目实际情况来自定义配置提交规则，例如很多时候我们不需要写长描述，公司内部的代码仓库也不需要管理 `issue`，那么可以把询问 `body` 和 `footer` 的步骤跳过（在 `.cz-config.js` 中修改成 `skipQuestions: ['body', 'footer']`）。

用 `git cz`试一下吧！

### 集成 commitlint 验证提交规范
在“代码规范”章节，我们已经讲到过，尽管制定了规范，但在多人协作的项目中，总有些人依旧我行我素，因此提交代码这个环节，我们也增加一个限制：只让符合 `Angular` 规范的 `commit message` 通过，我们借助 `@commitlint/config-conventional` 和 `@commitlint/cli` 来实现。
1. 安装 `commitlint`

```js
npm i @commitlint/config-conventional @commitlint/cli -D
```
2. 配置 `commitlint`

创建 `commitlint.config.js` 文件 在项目根目录下创建 `commitlint.config.js` 文件，并填入以下内容：
```js
module.exports = { extends: ['@commitlint/config-conventional'] }
```

使用 `husky` 的 `commit-msg` hook 触发验证提交信息的命令

我们使用 `husky` 命令在 `.husky` 目录下创建 `commit-msg` 文件，并在此执行 `commit message` 的验证命令。
```js
npx husky add .husky/commit-msg "npx --no-install commitlint --edit $1"
```
若执行失败，则手动创建 `commit-msg` 文件：
```js
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

npx --no-install commitlint --edit
```
3. `commitlint` 验证

不符合规范的提交信息（就会报错）:
```js
git commit -m "test commitlint"
```
符合规范的提交信息：
```js
git commit -m "test: commitlint test"
```

因为已在项目中集成 `commitizen`，建议大家用 `git cz` 来代替 `git commit` 提交代码，可以保证提交信息规范。

## 自动部署
到了这一步，我们已经在项目中集成`代码规范约束`、`提交信息规范约束`，从而保证我们远端仓库（如 `GitHub`、`GitLab`、`Gitee` 仓库等）的代码都是高质量的。

本项目是要搭建一套规范的`前端工程化`环境，为此我们使用 `CI`（Continuous Integration 持续集成）来完成项目最后的部署工作。

常见的 `CI` 工具有 `GitHub Actions`、`GitLab CI`、`Travis CI`、`Circle CI` 等。

这里，我们使用 `GitHub Actions`。

### 什么是 GitHub Actions
`GitHub Actions` 是 `GitHub` 的持续集成服务，持续集成由很多操作组成，比如`抓取代码`、`运行测试`、`登录远程服务器`、`发布到第三方服务`等等，`GitHub` 把这些操作称为 `actions`。

### 创建 GitHub Token
创建一个有 `repo` 和 `workflow` 权限的 [GitHub Token](https://github.com/settings/tokens/new)
- Note ： vue3-depoly-token
- workflow ： √
- Generate token

注意：新生成的 `Token` 只会显示一次，保存起来，后面要用到。如有遗失，重新生成即可。

### 在仓库中添加 secret
将上面新创建的 `Token` 添加到 `GitHub` 仓库的 `Secrets` 里，并将这个新增的 `secret` 命名为 `VUE3_PROJECT_DEPLOY` （名字无所谓，看你喜欢）。

步骤：仓库 -> `settings` -> `Secrets` -> `New repository secret`。

{% note warning %}
注意：新创建的 `secret` `VUE3_PROJECT_DEPLOY` 在 `Actions` 配置文件中要用到，两个地方需保持一致！
{% endnote %}

### 创建 Actions 配置文件
1. 在项目根目录下创建 `.github` 目录。
2. 在 `.github` 目录下创建 `workflows` 目录。
3. 在 `workflows` 目录下创建 `deploy.yml` 文件。

```js
name: deploy

on:
  push:
    branches: [master] # master 分支有 push 时触发

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      - name: Setup Node.js v14.x
        uses: actions/setup-node@v1
        with:
          node-version: '14.x'

      - name: Install
        run: npm install # 安装依赖

      - name: Build
        run: npm run build # 打包

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3 # 使用部署到 GitHub pages 的 action
        with:
          publish_dir: ./dist # 部署打包后的 dist 目录
          github_token: ${{ secrets.VUE3_PROJECT_DEPLOY }} # secret 名
          user_name: ${{ secrets.MY_USER_NAME }}
          user_email: ${{ secrets.MY_USER_EMAIL }}
          commit_message: Update Vite2.x + Vue3.x + TypeScript Starter # 部署时的 git 提交信息，自由填写

```
### 自动部署触发原理
这里说明一下：
1. `master` 分支存储项目源代码
2. `gh-pages` 分支存储打包后的静态文件

> `gh-pages` 分支，是 `GitHub Pages` 服务的固定分支，可以通过 `HTTP` 的方式访问到这个分支的静态文件资源。

当有新提交的代码 `push` 到 `GitHub` 仓库的 `master` 分支时，就会触发 `GitHub Actions`，在 `GitHub` 服务器上执行 `Action` 配置文件里面的命令，例如：安装依赖、项目打包等，然后将打包好的静态文件部署到 `GitHub Pages` 的 `gh-pages` 分支上（如果该分支不存在则会自动创建），最后，我们就能通过域名访问了。

使用自动部署，我们只需专注于项目开发阶段，任何重复且枯燥的行为都交由程序去完成，懒才是程序员第一生产力。

### 测试
将刚才新增的 `deploy.yml` 文件推到 `dev` 分支上，然后再合到 `master` 分支。

这时候去 `Github` 中查看 `master` 分支，代码确实推到 `master` 分支中了，这时候 `action` 报错，查看一下是代码和依赖的问题，将涉及到的 `$store` 和 `element-plus` 删除，再次推到 `master` ，这时候自动部署成功，并且新建一个新的静态文件分支 `github-pages`，里面存放的是打包后的静态文件。

## 最后
本文从`技术选项`到`架构搭建`、从`代码规范约束`到`提交信息规范约束`，再到`自动部署`，一步一步带领大家如何从一个最简单的前端项目骨架到规范的前端工程化环境，基本涵盖前端项目开发的整个流程，特别适合刚接触前端工程化的同学学习。