---
title: 图片url转file数据实现上传
author: wrrrrrr
index_img: 'https://miro.medium.com/max/1400/1*PPud-Z83-82NSwvzV9dnlg.png'
category: 技术
tags:
  - File
  - Upload
excerpt: 实现在线图片转file类型再进行文件上传
date: 2022-06-24 09:51:34
---

## 需求
现有一个图片 `url` 地址，前端需要将这张图片传给后端，上传是一个 `POST` 请求， `body` 请求类型是 `Form-data`，参数名是 `file` ，参数类型是 `file`

## 如何将一个图片url转成file?
1. 通过 `ajax` 请求获取图片 `blob` 数据，再转成 `file` 文件 
2. 通过 `canvas` 获取图片 `base64` 数据，再转成 `file` 文件 

## ajax
```js
getImageFileFromUrl(url, imageName) {
  return new Promise((resolve, reject) => {
    var blob = null;
    var xhr = new XMLHttpRequest();
    xhr.open('GET', url);
    xhr.setRequestHeader('Accept', 'image/jpeg');
    xhr.responseType = 'blob';
    xhr.onload = () => {
      blob = xhr.response;
      const imgFile = new File([blob], imageName, { type: 'image/jpeg' });
      resolve(imgFile);
    };
    xhr.onerror = e => {
      reject(e);
    };
    xhr.send();
  });
}
aiCheck(url) {
  this.getImageFileFromUrl(url, 'file')
  .then(res => {
    console.log('file文件', res);
  })
  .catch(() => {
  });
}
// 使用
this.aiCheck('https://www.baidu.com/img/flexible/logo/pc/result.png')
```

## base64
```js
// 图片转base64
imgToBase64(url, cb) {
  var canvas = document.createElement('canvas');
  var ctx = canvas.getContext('2d');
  var img = new Image();

  img.crossOrigin = 'Anonymous';
  img.onload = function() {
    canvas.height = img.height;
    canvas.width = img.width;
    ctx.drawImage(img, 0, 0);
    var dataURL = canvas.toDataURL('image/jpg');
    cb && cb(dataURL);
    canvas = null;
  };
  img.src = url;
}

// base64转file对象
base64toFile(base, filename) {
  var arr = base.split(',');
  var mime = arr[0].match(/:(.*?);/)[1];
  var suffix = mime.split('/')[1];
  var bstr = atob(arr[1]);
  var n = bstr.length;
  var u8arr = new Uint8Array(n);
  while (n--) {
    u8arr[n] = bstr.charCodeAt(n);
  }
  // 转换成file对象
  return new File([u8arr], `${filename}.${suffix}`, { type: mime });
}

// 使用
this.imgToBase64('https://www.baidu.com/img/flexible/logo/pc/result.png', base => {
  const res = this.base64toFile(base, 'file');
  console.log('file文件', res);
});
```

## 上传
```js
upload(file) {
  // 一定要 new FormData()
  const params = new FormData();
  params.append('file', file);
  // 上传接口，aiupload是本例自己封装的axios post 接口
  aiupload(params).then(res => {
    console.log(res)
  });
}
```