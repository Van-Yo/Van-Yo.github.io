---
title: 利用Form-data方式上传文件，二进制数据互相转换
author: 11LuckyBOY
index_img: 'https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9a40dac6b7ed4f16a52921dea7283e5e~tplv-k3u1fbpfcp-zoom-crop-mark:3024:3024:3024:1702.awebp?'
category: 技术
tags:
  - Javascript
  - Axios
excerpt: 文件上传，以及JS 的二进制家族：base64、File、Blob、ArrayBuffer 的关系
date: 2022-11-14 14:37:42
---

## 接口需求
![](1.jpg)

## 前端接口封装
```js
export function sendVoice(data) {
  return axios({
    url: '/server/device/sendVoice',
    method: 'post',
    data,
    headers: {
      // 表示上传的是文件,而不是普通的表单数据
      'Content-Type': 'multipart/form-data'
    }
  });
}
```

## 调用
```js
upload() {
  var blob = this.recLogLast.res.blob;  // 替换你自己的Blob数据
  var form = new FormData();
  form.append('file', blob, this.dialog.id + '.mp3'); // 替换你自己的文件名称
  sendVoice(form).then(res => {
    this.$message.success('发送成功');
  }).catch(err => {
    this.$message.error(err);
  });
}
```

## 小结
1. 配置 `axios` 接口，设置 `'Content-Type': 'multipart/form-data'` ，这很重要
2. 准备好 `Blob` 数据
3. `new FormData()`，发送数据

{% note info %}
那什么是 `Blob` 数据？它和File，base64，ArrayBuffer等有什么关系？
{% endnote %}

## File对象
>定义： 一个FileList 对象通常来自于一个 HTML input 元素的 files 属性,你可以通过这个对象访问到用户所选择的文件，或者拖拽文件；File 对象是特殊类型的 Blob，且可以用在任意的 Blob 类型的 context 中。

`<input type="file" id="upload" @change="choose">` 当你选择一张图片时，`$("#upload").files[0]` 会返回一个对象，或者利用 `element ui` 上传文件，也能拿到 `File`。
![](2.jpg)

## base64
>定义：Base64就是一种基于64个可打印字符来表示二进制数据的方法

![](3.jpg)

## ArrayBuffer
>定义：ArrayBuffer对象、TypedArray对象、DataView对象是JavaScript操作二进制数据的一个接口。

![](4.jpg)

## Blob
>定义：Blob 对象表示一个不可变、原始数据的类文件对象

`File`接口基于`Blob`，继承了 `blob` 的功能并将其扩展使其支持用户系统上的文件。
![](5.jpg)

### Blob URL 用于文件上传下载
我们可以通过window.URL.createObjectURL，接收一个Blob（File）对象，将其转化为Blob URL,然后赋给 a.download属性，然后在页面上点击这个链接就可以实现下载了，方法见最后源代码
![](6.jpg)

## Buffer
>Buffer是Node.js提供的对象，前端没有。 它一般应用于IO操作，例如接收前端请求数据时候，可以通过以下的Buffer的API对接收到的前端数据进行整合（这里就不介绍了）

## 附上互转源码
![](7.jpg)
```js
<template>
  <div class="container-area">
    <el-upload
      ref="upload"
      class="upload-demo"
      action="https://jsonplaceholder.typicode.com/posts/"
      :on-preview="handlePreview"
      :on-remove="handleRemove"
      :on-change="handleChange"
      :file-list="fileList"
      :auto-upload="false"
    >
      <el-button slot="trigger" size="small" type="primary">选取文件</el-button>
      <el-button style="margin-left: 10px;" size="small" type="success" @click="submitUpload">上传到服务器</el-button>
      <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
    </el-upload>
    <el-button slot="trigger" size="small" type="primary" @click="blobToBase64(file)">File转base64</el-button>

    <p>{{ base64 }}</p>
    <el-button slot="trigger" size="small" type="primary" @click="base64ToFile()">base64转File</el-button>
    <p />
    <el-button slot="trigger" size="small" type="primary" @click="base64ToBlob()">base64转Blob</el-button>
    <p>{{ blob }}</p>
    <el-button slot="trigger" size="small" type="primary" @click="blob2File(blob,'新文件')">Blob转File</el-button>
    <el-button slot="trigger" size="small" type="primary" @click="blob2ArrayBuffer(blob,'新文件')">Blob转ArrayBuffer</el-button>
    <el-button slot="trigger" size="small" type="primary" @click="downloadBlobUrl">Blob URL 用于文件上传下载</el-button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      fileList: [],
      base64: '',
      file: null,
      blob: ''
    }
  },
  methods: {
    submitUpload() {
    //   this.$refs.upload.submit()
      console.log(this.fileList)
    },
    handleRemove(file, fileList) {
      console.log(file, fileList)
    },
    handlePreview(file) {
      console.log(file)
    },
    handleChange(file, fileList) {
      this.file = file.raw
      console.log(this.file)
    },
    // blob => base64,File是特殊的blob
    blobToBase64(blob) {
      const reader = new FileReader()
      reader.addEventListener('load', () => {
        console.log(reader.result)
        this.base64 = reader.result
      })
      reader.readAsDataURL(blob)
    },
    // base64 => File
    base64ImgtoFile(baseUrl, filename = '新建文本文档') {
      const arr = baseUrl.split(',')
      const mime = arr[0].match(/:(.*?);/)[1]
      const suffix = mime.split('/')[0]
      const bstr = atob(arr[1])
      let n = bstr.length
      const u8arr = new Uint8Array(n)
      while (n--) {
        u8arr[n] = bstr.charCodeAt(n)
      }
      return new File([u8arr], `${filename}.${suffix}`, {
        type: mime
      })
    },
    // base64 => Blob
    dataURItoBlob(dataURI) {
      var byteString = atob(dataURI.split(',')[1])
      var mimeString = dataURI.split(',')[0].split(':')[1].split(';')[0]
      var ab = new ArrayBuffer(byteString.length)
      var ia = new Uint8Array(ab)
      for (var i = 0; i < byteString.length; i++) {
        ia[i] = byteString.charCodeAt(i)
      }
      return new Blob([ab], { type: mimeString })
    },
    base64ToFile() {
      const file = this.base64ImgtoFile(this.base64)
      console.log(file)
    },
    base64ToBlob() {
      const blob = this.dataURItoBlob(this.base64)
      this.blob = blob
      console.log(blob)
    },
    /**
     *  @param { blob } blob
     *  @param { string } fileName
     */
    // Blob => File
    blob2File(blob, fileName) {
      blob.lastModifiedDate = new Date()
      blob.name = fileName
      console.log(blob)
      return blob
    },
    // Blob => ArrayBuffer
    blob2ArrayBuffer() {
      const reader = new FileReader()
      reader.onload = function() {
        const content = reader.result
        console.log(content)
      }
      reader.readAsArrayBuffer(this.blob)
    },
    printRes(res) {
      console.log(res)
    },
    // Blob URL 用于文件上传下载
    downloadBlobUrl() {
      var url = window.URL.createObjectURL(this.blob)
      console.log(url)
      const link = document.createElement('a')
      link.style.display = 'none'
      link.href = url
      link.setAttribute('download', 'helloworld.txt')
      document.body.appendChild(link)
      link.click()
    }
  }
}
</script>

<style lang="scss" scoped>
.container-area{
}
</style>
```