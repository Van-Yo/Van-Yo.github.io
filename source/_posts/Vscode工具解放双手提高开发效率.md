---
uuid: 4d24bf49-3495-c857-bc48-2ba2571d8f1d
title: Vscode工具解放双手提高开发效率
categories: 技术
tags: 
- vscode
- vue
---
在开发过程中，如何又好又快地开发已经成为一个热点话题。在vscode中各式各样插件工具已经逐渐被开发和应用，本文旨分享一些现有的好用的开发插件或者方法。
## 代码片段
vscode - 设置 - 用户代码片段 - 新代码片段 - 输入代码段文件名 - 拷贝下面代码
```json
{
    "Print to console": {
        "prefix": "vue",
        "body": [
            "<template>",
            "\t<section class=\"template-area\"></section>",
            "</template>\n",
            "<script>",
            "export default {",
            "\tdata() {",
            "\t\treturn {};",
			"\t},",
			"\tcreated(){},",
            "\tmethods: {},",
            "\tcomponents: {}",
            "};",
            "</script>\n",
			"<style lang=\"less\" scoped>",
			".template-area{",
			"\tbackground-color: #fff;",
			"\theight: 100%;",
			"\toverflow: auto;",
			"}",
            "</style>",
			"$2"
        ],
        "description": "Vue code snippet"
    }
}
```
这样以后新建一个vue文件后，只需要输入vue，回车，就能得到vue模板最基础的代码：
```js
<template>
    <section class="template-area"></section>
</template>

<script>
export default {
    data() {
        return {};
    },
    created(){},
    methods: {},
    components: {}
};
</script>

<style lang="less" scoped>
.template-area{
    background-color: #fff;
    height: 100%;
    overflow: auto;
}
</style>
```

## AutoScssStruct4Vue插件
有个小伙伴提到写新页面的时候，`template`大概布局写完后，对着 `template`结构写`scss`是件比较耗时耗力的事情，如果能作出一个自动依据`template`结构生成`scss`文件的`vscode插件`就好了,AutoScssStruct4Vue插件就这么产生了，在`vscode`扩展市场直接安装即可。
当写完template代码后，右击，选择AutoScssStruct就可以生成你想要的css了，而且可以重复右击编译，实现更新的效果，会兼容之前写好的css代码。例如：
```js
<template>
    <section class="template-area">
        <div class="container">
            <div class="header"></div>
            <div class="content">
                <div class="main-title"></div>
            </div>
            <div class="footer"></div>
        </div>
    </section>
</template>

<script>
export default {
    data() {
        return {};
    },
    created(){},
    methods: {},
    components: {}
};
</script>

<style lang="less" scoped>
</style>
```
右击AutoScssStruct后：
```js
<template>
    <section class="template-area">
        <div class="container">
            <div class="header"></div>
            <div class="content">
                <div class="main-title"></div>
            </div>
            <div class="footer"></div>
        </div>
    </section>
</template>

<script>
export default {
    data() {
        return {};
    },
    created(){},
    methods: {},
    components: {}
};
</script>

<style lang="less" scoped>
.template-area {
    .container {
        .header {
        }
        .content {
            .main-title {
            }
        }
        .footer {
        }
    }
}
</style>
```