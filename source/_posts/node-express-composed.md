---
title: 原生Nodejs封装类似Express框架
author: Revan
index_img: /img/nodejs-expressjs.png
category: 技术
tags:
  - Nodejs
  - Express
excerpt: 之前也是看别人教程，学习Nodejs，并且利用Express框架为自己写过接口，但终究感觉没有系统性地学习Nodejs。那这次趁有时间和有动力，就再系统深入性地学习一下，就从原生Nodejs封装类似Express框架开始吧！
date: 2021-02-04 15:57:14
---

## Express框架
[Express](https://expressjs.com/zh-cn/) 是一种保持最低程度规模的灵活 Node.js Web 应用程序框架，为 Web 和移动应用程序提供一组强大的功能。其使用也是特别简单：
```js
const express = require('express')
const app = express()
 
app.get('/', function (req, res) {
  res.send('Hello World')
})
 
app.listen(3000)
```
那如何封装一个类似的框架呢？开始吧！

## 封装服务
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

## 调用服务
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

## 总结
核心代码大致如上，项目全部可查看[nodejs-express-composed](https://github.com/Van-Yo/nodejs-express-composed)