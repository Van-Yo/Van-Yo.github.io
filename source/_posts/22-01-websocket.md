---
title: 初探websocket
author: 11LuckyBOY
index_img: 'https://koenig-media.raywenderlich.com/uploads/2020/08/Screenshot_2020-08-10_at_15.08.19.png'
category: 技术
tags:
  - WebSocket
  - Node
excerpt: 初次接触 WebSocket 的人，都会问同样的问题：我们已经有了 HTTP 协议，为什么还需要另一个协议？它能带来什么好处？答案很简单，因为 HTTP 协议有一个缺陷：通信只能由客户端发起。
date: 2022-01-24 14:22:24
---
举例来说，我们想了解今天的天气，只能是客户端向服务器发出请求，服务器返回查询结果。 `HTTP` 协议做不到服务器主动向客户端推送信息。
这种单向请求的特点，注定了如果服务器 `有连续的状态变化` ，客户端要获知就非常麻烦。我们只能使用`"轮询"`：每隔一段时候，就发出一个询问，了解服务器有没有新的信息。最典型的场景就是聊天室。
轮询的效率低，非常浪费资源（因为必须不停连接，或者 `HTTP` 连接始终打开）。因此，工程师们一直在思考，有没有更好的方法。 `WebSocket` 就是这样发明的。

## 入门
### 1.服务端
代码如下，监听 `3001` 端口。当有新的连接请求到达时，打印日志，同时每隔 `3秒` 向客户端发送消息，消息内容是当前时间戳。
提前安装两个库： `express`, `ws`
```js
// server.js
const express = require('express');
const app = express();
// 设置静态文件夹
app.use(express.static(__dirname));
// 监听3001端口
app.listen(3001);
// =============================================
// 开始创建一个websocket服务
const Server = require('ws').Server;
// 这里是设置服务器的端口号，和上面的3001端口不用一致
const ws = new Server({ port: 9999 });

// 监听服务端和客户端的连接情况
ws.on('connection', function(socket) {
    // 监听客户端发来的消息
    socket.on('message', function(msg) {
        console.log(msg);   // 这个就是客户端发来的消息
        // 来而不往非礼也，服务端也可以发消息给客户端
        setInterval(()=>{
            socket.send(`当前时间是： ${new Date().getTime()}`);
        },3000)
    });
});
```

### 2.客户端
`index.html`代码如下，向 `3001` 端口发起 `WebSocket` 连接。连接建立后，向服务端发送消息。接收到来自服务端的消息后，同样打印日志，并在页面中渲染。
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>

<body>
    <script>
        // 只需要new一下就可以创建一个websocket的实例
        // 我们要去连接ws协议
        // 这里对应的端口就是服务端设置的端口号9999
        let ws = new WebSocket('ws://localhost:9999');

        // onopen是客户端与服务端建立连接后触发
        ws.onopen = function () {
            ws.send('hello,server!');
        };

        // onmessage是当服务端给客户端发来消息的时候触发
        ws.onmessage = function (res) {
            console.log(res);   // 打印的是MessageEvent对象
            // 真正的消息数据是 res.data
            console.log(res.data);
            var childNode = document.createElement('p');
            childNode.innerText = res.data;
            document.body.appendChild(childNode);   // 添加节点
        };
    </script>
</body>

</html>
```

### 3.运行

1. 运行 `server.js`，3001端口的服务就被打开了

```
node ./server.js
``` 


2. 运行 `index.html` 到浏览器，页面隔3秒就会新增一行当前时间,如下：

当前时间是： 1643006471350
当前时间是： 1643006474353
当前时间是： 1643006477367
当前时间是： 1643006480373
当前时间是： 1643006483387
当前时间是： 1643006486389
当前时间是： 1643006489401
当前时间是： 1643006492410

`表示服务器在不停地向客户端主动发送数据`

3. 查看请求头

![](socket1.jpg)

