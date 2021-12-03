---
title: 新增 SSH 密钥到 GitHub 帐户
author: Revan
index_img: /img/new-journey.png
category: 技术
tags:
  - Github
  - SourceTree
excerpt: 很久没更新博客了，最近想重拾起来，回忆了一下当时更新博客的步骤，发现在部署到Github上出现认证问题，查阅了一下资料，发现Github早已停止Git客户端账号密码登录远程推送...
date: 2021-12-02 16:34:35
---
## 配置SSH通道访问Github

### 检测本机本地机是否含有ssh设置
1. 在 `Git Bush` 中输入指令 `ls -al ~/.ssh` 可以将本地磁盘 `C:\Users\Administrator\.ssh` 文件夹中的文件都列出来，其中 `id_rsa` 表示密钥， `id_rsa.pub` 表示公钥。
![](ssh.jpg)
![](ssh1.jpg)
2. 如果没有 `C:\Users\Administrator\.ssh` 的文件夹表示之前没添加过密钥，手动新建 `C:\Users\Administrator\.ssh` 文件夹。

### 使用git base生成心的ssh key
指令输入 `ssh-keygen -t rsa -C "xxxxxx@xxx.com"` (填写自己有效的邮箱)，根据提示输入，也可简单三下回车键，生成上一步需要的密钥和公钥文件

### 添加ssh key到GitHub
1. 登录 `GitHub` 系统；点击右上角账号头像的“▼”→Settings→SSH and GPG keys→New SSH key
![](github.jpg)
2.  `Title` 随便填， `Key` 复制生成的 `id_rsa.pub` 的公钥内容粘贴进去即可
![](add.jpg)
3. 这样推拉远程仓库就不需要通过账号密码了，但是在 `SourceTree` 中在 `push` 时却显示密钥认证失败，转下面的解决方案。

## SourceTree push 报错SSH密钥认证失败
1. 【工具】-【选项】-【一般】
2. 因为 `sourceTree` 默认SSH客户端配置的SSH客户端 是 `PuTTY/Plink`,把它选择为 `OpenSSH SSH` 密钥自动会适配到当前 `id_rsa` 文件，点击确定。
![](sourceTree.jpg)