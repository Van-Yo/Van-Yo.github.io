---
uuid: dc3249ce-c7f2-7624-a51e-8c89e11e4e8d
title: Vue默认图片设置
categories: 技术
tags: 
- vue
---
在项目中经常会遇到一个关于图片的问题：如何设置默认图片？真实场景：获取某个区的一级医院列表，当从后端接口拿到数据后，里面每个医院对象里面都会有一个hospitalPicture字段，该字段可能有值可能无值，又可能是有效值或无效值，如何在不影响页面显示上配置好默认图片？
## 需求
从后端接口取得图片地址，如果有图片地址，则正常显示该图片地址，反之，则显示默认图片地址；其次，虽然有图片地址，但是该地址报错即获取不到，也显示默认图片。

## 解决方案
- js

```js
<script>
    import defaultImg from '@assets/image/hospitalOrder/icon_hospital.png';
    export default {
        data() {
            return {
                defaultImg : `this.src="${defaultImg}"`
            };
        },
        //...
    }
</script>
```
- template

```js
<div class="hospital-img">
      <img :src="hospitalPicture ? hospitalPicture : ''" :onerror="defaultImg"/>
</div>
```

<b>备注</b>：由于工程是vue的前端工程，就需要引入图片，而不能直接使用图片的路径，直接使用路径图片将不会渲染。
## 总结
> 当hospitalPicture为空时，src 为空，此时会执行  :onerror="defaultImg"；而且，当获取hospitalPicture地址报错时，此时也会执行  :onerror="defaultImg"。