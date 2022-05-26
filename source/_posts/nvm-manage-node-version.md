---
title: nvm管理node包版本
author: 11LuckyBOY
index_img: 'https://www.howtogeek.com/wp-content/uploads/2020/10/nvm.jpg'
category: 技术
tags:
  - Node
  - Nvm
  - Vite
excerpt: 在玩vue3+vite的时候，启动项目的时候报错Cannot find module ‘worker_threads’，查了一下发现是因为个人电脑node版本太低，查看了一下版本，考虑到公司项目的node版本也比较低，妄自升级本机node会造成问题，于是用nvm来管理node包，对不同项目使用不同版本的node版本
date: 2022-05-26 13:41:02
---

> 在玩 `vue3+vite` 的时候，启动项目的时候报错 `Cannot find module ‘worker_threads’` ，查了一下发现是因为个人电脑 `node` 版本太低，查看了一下版本，考虑到公司项目的 `node` 版本也比较低，妄自升级本机 `node` 会造成问题，于是用 `nvm` 来管理 `node` 包，对不同项目使用不同版本的 `node` 版本

### 下载安装
{% btn https://github.com/coreybutler/nvm-windows/releases, nvm %}

安装后可查看环境变量
![](1.jpg)

### 使用
`nvm` 常用命令
- 查看可安装版本

```
nvm ls
```

- 安装

```
nvm install 16.15.0
```

- 切换

```
nvm use 16.15.0
```

- 卸载

```
nvm uninstall 16.15.0
```
![](2.jpg)

## 注意
1. 切换命令使用最好在管理员模式下进行
2. 卸载命令要关闭当前 `node` 项目  

