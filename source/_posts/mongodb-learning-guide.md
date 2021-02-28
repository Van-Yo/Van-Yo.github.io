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
文件路径：`mongodb安装目录\bin\mongod.cfg`，找到`#security`，改成如下配置，表示要开启访问控制。

**注意**：最好是完全复制下面代码，因为`mongodb`配置文件严格对待`空格符`和`缩进符`。
```js
security:
  authorization: enabled
```

### 第三步：重新启动mongodb
```js
// 快捷键win+R
// 输入services.msc
// 找到mongodb服务右击重启
```

### 第四步：用超级管理员账户链接数据库
重启后若再次输入`mongo`连接数据库`show dbs`时就会报错或者不显示数据库列表了，必须使用`账号密码`进行登录
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

## Nodejs调用Mongodb驱动
### 第一步：初始化项目
```js
npm init -y
```
### 第二步：安装mongodb包
```js
npm i mongodb -S
```
### 第三步：连接数据
app.js
```js
const { MongoClient } = require("mongodb");
// 数据库信息：账号密码地址
const uri = "mongodb://admin:123456@127.0.0.1:27017"

const client = new MongoClient(uri,{ useUnifiedTopology: true });

async function run(){
    try {
        // 连接
        await client.connect();
        // 切换对应的数据库和集合
        const database = client.db('czschool');
        const collection = database.collection('teacher');
        // 对应的数据库操作：增删改查
        const query = { name: 'revan' };
        const person = await collection.findOne(query);
        console.log(person);
    }finally{
        // 关闭数据库连接
        await client.close()
    }
}
// 启动
run().catch(console.dir)
```
### 第四步：启动
在项目终端输入`node app.js`
```js
D:\Work\Test\node-server-learning>node app.js
```
结果：
```js
{ _id: 603a0f729ce5d7b58f23fea0,
  name: 'revan',
  age: 18,
  ic_no: 1 }
```