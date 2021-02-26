---
title: mongodb学习指南
author: Revan
index_img: /img/mongo.jpg
category: 技术
tags:
  - Mongodb
excerpt: Mongodb学习指南
date: 2021-02-26 15:24:45
---

## 权限认证
`MongoDB`刚完成安装的时候，默认是`不需要`进行用户验证，直接可以连接mongod服务的实例，对数据库进行各种`读写`操作，执行各种命令，这样的好处是方便进行测试。在`生产环境`部署`MongoDB`服务实例时，就必须开启`安全认证`，为数据库连接设置管理账号。
### 第一步：创建超级管理用户
在开启安全认证之前，我们首先要建立一个`超级管理员账号`，可进行`其它用户`的`添加`，`删除`，`修改`操作。`root`角色的用户可以做`MongoDB`的各种操作，没有任何限制，类似于`mysql`的`root`账号。
```js
// 切换到admin数据库
use admin
```
```js
// 创建超级管理用户
db.createUser({
  user:'admin',
  pwd:'123456',
  roles:[{role:'root',db:'admin'}]
})
```
### 第二步：修改mongodb数据库配置文件
文件路径：mongodb安装目录\bin\mongod.cfg
```js
// #security 改成：
security:
	authorization:enabled
```

### 第三步：重新启动mongodb
```js
// 快捷键win+R
// 输入services.msc
// 找到mongodb服务右击重启
```

### 第四步：用超级管理员账户链接数据库
重启后若再次输入`mongo`连接数据库`show dbs`时就会报错了，必须使用`账号密码`进行登录
```js
// 本地连接：
mongo admin -u 用户名 -p 密码
// 远程数据库连接，例如：
mongo 192.168.1.200:27017/test -u 用户名 -p 密码
```

### 第五步：给每一个数据库创建一个用户权限
假如公司来了一个新员工，不能把超级管理员管理权限给他，需要给他一个数据库权限，他只能操作该数据库，不能对其他数据库进行增删改查。
```js
// 切换到该数据库
use eggcms
```
```js
// 创建用户权限
db.createUser(
  {
    user:"eggadmin",
    pwd:"123456",
    roles:[{role:"dbOwner",db:"eggcms"}]
  }
)
```
### node连接数据库的时候需要配置账户密码
```js
const url = 'mongodb://admin:123456@localhost:27017';
```

### 常用命令
```js
show users   // 查看当前库下的用户
db.dropUser("eggadmin")   // 删除用户
db.updateUser("admin",{pwd:"password"});    //修改用户密码
db.auth("admin","password");    //密码认证
```