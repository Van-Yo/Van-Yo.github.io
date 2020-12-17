---
title: hello-fluid
date: 2020-12-17 10:19:28
index_img: https://rmt.dogedoge.com/fetch/fluid/storage/hello-fluid/cover.png?w=480&fmt=webp
category: 主题示例
tags:
  - 示例
  - Fluid
math: true
mermaid: true
sticky: 100
---

>欢迎体验 [Fluid](https://github.com/fluid-dev/hexo-theme-fluid) ，这是一款 Material Design 风格的 Hexo 主题，以简约的设计帮助你专注于写作，本篇文章可预览主题的样式及功能。

```
>欢迎体验 [Fluid](https://github.com/fluid-dev/hexo-theme-fluid) ，这是一款 Material Design 风格的 Hexo 主题，以简约的设计帮助你专注于写作，本篇文章可预览主题的样式及功能。
```

<!-- more -->

## H2 标题

```
## H2 标题
```

### H3 标题

```
### H3 标题
```

#### H4 标题

```
#### H4 标题
```

**粗体**

```
**粗体**
```

_斜体_

```
_斜体_
```

## 代码

行内代码：`$ hexo new post "My New Post"`

```
行内代码：`$ hexo new post "My New Post"`
```

代码高亮使用的是 highlight.js，支持 185 种语言和 91 种高亮样式：

```js
const Pending = Symbol('Pending'); // 进行中
const Fulfilled = Symbol('Fulfilled'); // 已成功
const Rejected = Symbol('Rejected'); // 已失败
class Promise {
    constructor(executor){
        this.status = Pending;//存储 Promise 的状态
        const resolve = () =>{
            // 只有当状态为 Pending 才会改变，来保证一旦状态改变就不会再变。
            if(this.status === Pending){
                this.status = Fulfilled;
            }
        };
        const reject = () =>{
            // 只有当状态为 Pending 才会改变，来保证一旦状态改变就不会再变。
            if(this.status === Pending){
                this.status = Rejected;
            }
        };
        executor(resolve,reject);
    },
}
```

## 表格

| Header 1 | Header 2 | Header 3 |
| --- | --- | --- |
| Key 1 | Value 1 | Comment 1 |
| Key 2 | Value 2 | Comment 2 |
| Key 3 | Value 3 | Comment 3 |

```
| Header 1 | Header 2 | Header 3 |
| --- | --- | --- |
| Key 1 | Value 1 | Comment 1 |
| Key 2 | Value 2 | Comment 2 |
| Key 3 | Value 3 | Comment 3 |
```

## 列表

### 有序列表

Fluid 相较于其他主题的优势：

1. 设计遵循简洁至上，同时具有轻快的体验，和优雅的颜值；
2. 提供大量定制化配置项，使每个用户使用该主题都能具有独特的样式；
3. 响应式页面，适配手机、平板等设备；

```
1. 设计遵循简洁至上，同时具有轻快的体验，和优雅的颜值；
2. 提供大量定制化配置项，使每个用户使用该主题都能具有独特的样式；
3. 响应式页面，适配手机、平板等设备；
```

### 无序列表

Fluid 功能特性：

- 图片懒加载
- 自定义代码高亮方案
- 内置多语言
- 支持多款评论插件
- 支持使用数据文件存放配置
- 自定义静态资源 CDN
- 内置文章搜索
- 页脚备案信息
- 网页访问统计
- 支持 LaTeX 数学公式
- 支持 mermaid 流程图
- 音乐播放器

```
- 图片懒加载
- 自定义代码高亮方案
- 内置多语言
- 支持多款评论插件
- 支持使用数据文件存放配置
- 自定义静态资源 CDN
- 内置文章搜索
- 页脚备案信息
- 网页访问统计
- 支持 LaTeX 数学公式
- 支持 mermaid 流程图
- 音乐播放器
```

## 图片

![](https://rmt.dogedoge.com/fetch/fluid/storage/post.png?w=1280&fmt=webp)

```
![](https://rmt.dogedoge.com/fetch/fluid/storage/post.png?w=1280&fmt=webp)
```

## 内置 Tag 插件

内置了一些 Tag 插件，用于实现 Markdown 不容易生成的样式，具体使用方式请见 [用户指南](https://hexo.fluid-dev.com/docs/guide/#tag-%E6%8F%92%E4%BB%B6)。

```
内置了一些 Tag 插件，用于实现 Markdown 不容易生成的样式，具体使用方式请见 [用户指南](https://hexo.fluid-dev.com/docs/guide/#tag-%E6%8F%92%E4%BB%B6)。
```

### 便签

{% note info %}
这里可以写文字 或者 `markdown`
{% endnote %}

```
{% note info %}
这里可以写文字 或者 `markdown`
{% endnote %}
```

{% note warning %}
这里可以写文字 或者 `markdown`
{% endnote %}

```
{% note warning %}
这里可以写文字 或者 `markdown`
{% endnote %}
```

{% note primary %}
这里可以写文字 或者 `markdown`
{% endnote %}

```
{% note primary %}
这里可以写文字 或者 `markdown`
{% endnote %}
```

### 行内标签

{% label info @行内标签 %} {% label warning @行内标签 %} {% label primary @行内标签 %}

```
{% label info @行内标签 %} {% label warning @行内标签 %} {% label primary @行内标签 %}
```

### 勾选框

{% cb 主要是解决一些 Renderer 不支持勾选, true %}

```
{% cb 主要是解决一些 Renderer 不支持勾选, true %}
```

### 按钮

{% btn javascript:;, 支持链接 %}

```
{% btn javascript:;, 支持链接 %}
```

### 组图

{% gi 5 3-2 %}
  ![](https://rmt.dogedoge.com/fetch/fluid/storage/hello-fluid/cover.png?w=480&fmt=webp)
  ![](https://rmt.dogedoge.com/fetch/fluid/storage/hello-fluid/cover.png?w=480&fmt=webp)
  ![](https://rmt.dogedoge.com/fetch/fluid/storage/hello-fluid/cover.png?w=480&fmt=webp)
  ![](https://rmt.dogedoge.com/fetch/fluid/storage/hello-fluid/cover.png?w=480&fmt=webp)
  ![](https://rmt.dogedoge.com/fetch/fluid/storage/hello-fluid/cover.png?w=480&fmt=webp)
{% endgi %}

```
{% gi 5 3-2 %}
  ![](https://rmt.dogedoge.com/fetch/fluid/storage/hello-fluid/cover.png?w=480&fmt=webp)
  ![](https://rmt.dogedoge.com/fetch/fluid/storage/hello-fluid/cover.png?w=480&fmt=webp)
  ![](https://rmt.dogedoge.com/fetch/fluid/storage/hello-fluid/cover.png?w=480&fmt=webp)
  ![](https://rmt.dogedoge.com/fetch/fluid/storage/hello-fluid/cover.png?w=480&fmt=webp)
  ![](https://rmt.dogedoge.com/fetch/fluid/storage/hello-fluid/cover.png?w=480&fmt=webp)
{% endgi %}
```

### 脚注

以下是脚注演示[^1]：

如果你有 Fluid 主题或 Hexo 博客相关的文章，可以通过 Pull Request 方式投稿[^2]。

[^1]: 脚注演示
[^2]: 投稿具体详见[https://github.com/fluid-dev/hexo-fluid-blog](https://github.com/fluid-dev/hexo-fluid-blog)