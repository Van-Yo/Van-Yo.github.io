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
**注意**，这里项目前提有git，不然无法运行。

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