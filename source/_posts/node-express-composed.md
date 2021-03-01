---
title: 原生Nodejs封装类似Express框架
author: Revan
index_img: /img/nodejs-expressjs.png
category: 技术
tags:
  - Nodejs
  - Express
excerpt: Express是一种保持最低程度规模的灵活 Node.js Web 应用程序框架，为 Web 和移动应用程序提供一组强大的功能。本文先从从原生Nodejs封装类似Express框架开始，然后再系统性地对其进行研究！
date: 2021-02-04 15:57:14
---

## Express简介
[Express](https://expressjs.com/zh-cn/) 是一种保持最低程度规模的灵活 Node.js Web 应用程序框架，为 Web 和移动应用程序提供一组强大的功能。其使用也是特别简单：
```js
const express = require('express')
const app = express()
 
app.get('/', function (req, res) {
  res.send('Hello World')
})
 
app.listen(3000)
```

## Express框架简易封装
如何利用原生Nodejs封装一个类似的框架呢？开始吧！
### 封装
./module/route.js
```js
const url = require('url')
const fs = require('fs')
const path = require('path')

// 根据后缀名获取文件类型
function getFileMime(extname){
    let data = fs.readFileSync('./data/mime.json');
    let mimeObj = JSON.parse(data.toString());
    return mimeObj[extname]
}

// 静态web服务的方法
function initStatic(req,res,staticPath){
    let pathname = url.parse(req.url).pathname;
    pathname = pathname == '/' ? '/index.html' : pathname;
    let extname = path.extname(pathname).slice(1);

    try{
        let data = fs.readFileSync('./'+staticPath + pathname);
        console.log(data)
        if(data){
            console.log(extname)
            let mime = getFileMime(extname);
            res.writeHead(200,{'content-Type':''+mime+';charset="utf-8"'})
            res.end(data);
        }
    }catch(err){

    }
}

let server = () => {
    // 把 get 和 post 分开
    let G = {
        _get : {},
        _post : {},
        staticPath : 'static'   // 静态web目录
    }
    

    let app = function(req,res){
        // 配置静态web目录
        initStatic(req,res,G.staticPath)

        let pathname = url.parse(req.url).pathname;
        let method = req.method.toLowerCase();
        if(G['_'+method][pathname]){
            if(method == 'get'){
                G._get[pathname](req,res)
            }else if(method == 'post'){
                // 获取post数据，把他绑定到req.body
                let postData = '';
                req.on('data',(chunk)=>{
                    postData+=chunk;
                })
                req.on('end',()=>{
                    req.body = postData;
                    G._post[pathname](req,res);
                })
            }
            
        }else{
            res.writeHead(404, {'Content-Type': 'text/html;charset="utf-8"'});
            res.end('页面不存在');
        }
    }

    app.get = function(str,cb){
        G._get[str] = cb
    }

    app.post = function(str,cb){
        G._post[str] = cb
    }

    // 静态web服务
    app.static = function(staticPath){
        G.staticPath = staticPath;
    }

    return app
}

module.exports = server()
```

### 调用服务
./express_router
```js
const http = require('http');
const app = require('./module/route');
const ejs = require('ejs')

/**
 * 原先http-server是这么写的
 * 也就是每次使用服务都会执行里面的function方法
*/
// http.createServer(function (request, response) {
// 	response.writeHead(200, {'Content-Type': 'text/plain'});
// 	response.end('Hello World');
// }).listen(8081);

/**
 * 对上面的function进行改造，改成我们想要执行的东西
 * 注册web服务
 * 以后每次使用服务都会执行app方法
*/
http.createServer(app).listen(8088);
console.log('Server running at http://127.0.0.1:8088/');

// 静态web服务
app.static('public')

// 配置路由
app.get('/',(req,res)=>{
	res.writeHead(200, {'Content-Type': 'text/html;charset="utf-8"'});
	res.end('home');
})

// 配置路由
app.get('/login',(req,res)=>{
	ejs.renderFile('./views/login.ejs', {name:'提交'}, function(err, str){
		res.writeHead(200, {'Content-Type': 'text/html;charset="utf-8"'});
		res.end(str);
	});
	
})

// 配置路由
app.post('/doLogin',(req,res)=>{
	console.log(req.body)
	res.writeHead(200, {'Content-Type': 'text/html;charset="utf-8"'});
	res.end(JSON.stringify(req.body));
})
```

### 总结
核心代码大致如上，项目全部可查看[nodejs-express-composed](https://github.com/Van-Yo/nodejs-express-composed)

## Express学习
接下来开始Express学习
### 路由、动态路由、get传值
```js
const express = require('express')
const app = express()

// get请求
// http://localhost:3000/
app.get('/',(req, res) => {
    res.send('Hello World')
})

// post请求
// Postman测试
app.post('/submit',(req,res)=>{
    res.send('提交成功')
})

// put请求
// Postman测试
app.put('/edit',(req,res)=>{
    res.send('修改成功')
})

// selete请求
// Postman测试
app.delete('/delete',(req,res)=>{
    res.send('删除成功')
})

// 动态路由
// http://localhost:3000/getArticle/123456
app.get('/getArticle/:id',(req,res)=>{
    let id = req.params.id;
    if(id){
        res.send('文章id是：'+id)
    }
})

// get传值
// http://localhost:3000/getInfo?name=hello&content=helloWorld
app.get('/getInfo',(req,res)=>{
    let obj = req.query;
    let {name,content} = obj;
    res.send(`文章名是：${name}，内容是：${content}`)
})

app.listen(3000)
```

### ejs模板引擎
安装ejs包
```js
npm install ejs -S
```
/app.js
```js
const express = require('express')
const app = express()
// 配置模板引擎
app.set("view engine","ejs")

// ejs模板引擎
// http://localhost:3000/
app.get('/',(req, res) => {
    let title = "你好ejs"
    // 默认保存在/views
    res.render("index",{
        title
    })
})

app.listen(3000)
```
/views/index.ejs
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ejs模板引擎</title>
</head>
<body>
    <h1>ejs模板引擎</h1>
    <p><%=title%></p>
</body>
</html>
```

### 托管静态文件
新建静态文件目录和静态文件/static/css/base.css
```css
h1{
    background: red;
}
```
/app.js
```js
// 配置静态web目录
app.use(express.static("static"))
```
/views/index.ejs
```html
<link rel="stylesheet" href="/css/base.css">
```
此时访问 http://localhost:3000/css/base.css 或者 http://localhost:3000/ 就可以看到静态文件奏效了。

### 中间件
1.**应用级中间件（用于权限判断）**：在请求路由前会`优先`进入该中间件，`next()`后再请求对应路由
```js
app.use((req,res,next)=>{
    next()
})
```

2.**路由级中间件（用的比较少）**：假如有下面两个路由，没有加上`next()`，此时，访问 http://localhost:3000/getArticle/hello 是不会匹配第二个路由的，但是如果在第一个路由中加上`next()`，那两个路由就都会匹配到了，最终以匹配到的最后一个路由作为结果。
```js
app.get('/getArticle/hello',(req,res)=>{
    res.send('文章名称是hello')
    next()
})
// 动态路由
app.get('/getArticle/:id',(req,res)=>{
    let id = req.params.id;
    if(id){
        res.send('文章id是：'+id)
    }
})
```

3.**错误处理中间件**：放在所有路由的最后
```js
app.use((req,res,next)=>{
    res.status(404).send("404")
})
```

4.**内置中间件**：配置静态web目录
```js
// 配置静态web目录
app.use(express.static("static"))
```

5.**第三方中间件**：假如`body-parser`，获取`post`数据就可以使用`req.body`了
安装`body-parser`
```js
npm install body-parser -S
```
配置：
```js
const bodyParser = require('body-parser')

// create application/json parser
app.use(bodyParser.json())
 
// create application/x-www-form-urlencoded parser
app.use(bodyParser.urlencoded({extended:false}))
```
使用：
```js
app.post('/submit',(req,res)=>{
    res.send(JSON.stringify(req.body))
})
```
可以用`Postman`简单测试

### Cookie
`Cookie`是`服务器`发送到用户`浏览器`并保存在本地的一小块数据，它会在浏览器下次向同一服务器再发起请求时被`携带`并发送到服务器上。`Express`可以使用`cookie-parser`来实现`Cookie`。

1.安装`cookie-parser`
```js
npm install cookie-parser -S
```

2.配置
```js
const express = require('express')
const app = express()
const cookieParser = require('cookie-parser')

app.use(cookieParser())

// 设置cookie
app.get('/doLogin',(req, res) => {
    res.cookie("username","revan",{maxAge:1000*60*60});
    res.send("设置cookie成功")
})

// 获取cookie
app.get('/getInfo',(req,res)=>{
    let username = req.cookies.username;
    res.send("您好！"+username);
})
```

3.使用
- 访问 http://localhost:3000/doLogin 页面会显示：设置cookie成功
- `F12`切到开发者工具，在`cookie`中也能看到`username`的`cookie`值
- 访问 http://localhost:3000/getInfo 页面会显示：您好！revan

4.参数说明
```js
export interface CookieOptions {
    maxAge?: number;    // 设置cookie的过期时间
    signed?: boolean;   // 是否加密，默认不加密
    expires?: Date;     // 设置一个具体的cookie过期日期
    httpOnly?: boolean; // true:只能在后端设置获取cookie
    path?: string;      // 可以访问cookie的路由：req.cookies.username
    domain?: string;    // 域(二级)共享cookie:{domain:'.joker.com'} => aaa.joker.com和 bbb.joker.com 共享cookie
    secure?: boolean;   // true:cookie在http协议中无效
    encode?: (val: string) => string;
    sameSite?: boolean | 'lax' | 'strict' | 'none';
}
```
**cookie加密：signed**
1. 配置加密密钥： `app.use(cookieParser('xxxxxx'))`
2. 设置加密cookie： `res.cookie("username","revan",{maxAge:1000*60*60,signed:true});`
3. 获取解密cookie： `req.signedCookies.username;`

### Session
`Session`的工作流程是基于`Cookie`的，当浏览器访问服务器并发送第一次请求时，服务器端会创建一个`session`对象，生成一个类似于`key`，`value`的键值对，然后将`key`（`cookie`）返回到浏览器（客户端），浏览器下次再访问时，携带`key`（`cookie`）找到对应的`session`（`value`）。

1.安装
```js
npm install express-session -S
```

2.配置
```js
const app = express()
const session = require('express-session')

app.use(session({
    secret: 'keyboard cat',     // 服务器端设置的session签名
    name : 'appkey',  //存储cookie的名称，在浏览器开发者模式cookie中可以查看
    resave: false,  // 默认
    saveUninitialized: true,    // 默认
    // cookie设置
    cookie: { 
        maxAge : 1000*60,
        secure: false 
    },
    rolling : true,     // 再次访问路由会重新设置cookie的maxAge
}))
```

3.设置Session
```js
app.get('/doLogin',(req, res) => {
    // 设置session
    req.session.username = "张三",
    res.send("登录");
})
```

4.获取Session
```js
app.get('/getInfo',(req,res)=>{
    if(req.session.username){
        let username = req.session.username;
        res.send("您好！"+username);
    }else{
        res.send("没有登录");
    }
})
```

5.销毁Session
```js
// 1.设置session的过期时间为0（会把所有的session都销毁）
req.session.cookie.maxAge = 0
// 2.销毁指定的session
req.session.username = ""
// 3.销毁session
req.session.destroy()
```
