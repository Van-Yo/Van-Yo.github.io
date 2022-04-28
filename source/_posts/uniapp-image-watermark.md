---
title: uniapp图片添加水印
author: 11LuckyBOY
index_img: 'https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fxuexi.leawo.cn%2Fuploads%2Fallimg%2F210310%2F0924153011-0.jpg&refer=http%3A%2F%2Fxuexi.leawo.cn&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1653703456&t=ba7376f7f7fa96afda5ca80be8b472db'
category: 技术
tags:
  - Uniapp
excerpt: 最近碰上个uniapp（安卓）拍照图片加水印的需求，方法逻辑倒是很简单，利用画布，先将图片画上，再将水印添上即可，但是设置画布动态宽高（图片宽高等比例）不行，从而导致图片要么被拉伸，挤压或者显示不全等。
date: 2022-04-28 10:03:17
---

先贴代码：
```js
<template>
	<view>
		<button type="primary" @tap="takePhoto">拍照</button>
		<image style="width: 100%;" mode="widthFix" :src="src" @click="preview"></image>
		<view style="position: absolute;top: -999999px;">
			<view><canvas :style="{ width: w, height: h }" canvas-id="firstCanvas"></canvas></view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			src: '',
			w: '',
			h: ''
		};
	},
	methods: {
		preview() {
			uni.previewImage({
				urls: [this.src],
				current: 0
			});
		},
		takePhoto() {
			var that = this;
			uni.chooseImage({
				count: 1,
				success(res) {
					uni.getImageInfo({
						src: res.tempFilePaths[0],
						success: ress => {
							// 显示加载框
							uni.showLoading({
								title: '水印制作中',
								mask: true
							});

							that.w = ress.width / 3 + 'px';
							that.h = ress.height / 3 + 'px';
              
                            // ********************************重要setTimeout********************************
							setTimeout(() => {
								let ctx = uni.createCanvasContext('firstCanvas'); /** 创建画布 */
								//将图片src放到cancas内，宽高为图片大小
								ctx.drawImage(res.tempFilePaths[0], 0, 0, ress.width / 3, ress.height / 3);
								ctx.setFontSize(18);
								ctx.setFillStyle('#8a2929');
								ctx.rotate((30 * Math.PI) / 180);
								let textToWidth = (ress.width / 3) * 0.5;
								let textToHeight = (ress.height / 3) * 0.3;
								ctx.fillText('我是水印', textToWidth, textToHeight);
								/** 除了上面的文字水印，这里也可以加入图片水印 */
								//ctx.drawImage('/static/watermark.png', 0, 0, ress.width / 3, ress.height / 3)
								ctx.draw(false, () => {
									uni.canvasToTempFilePath({
										canvasId: 'firstCanvas',
										success: res1 => {
											that.src = res1.tempFilePath;
											uni.hideLoading();
										}
									});
								});
							}, 500);
						}
					});
				}
			});
		}
	}
};
</script>

<style></style>
```

{% note warning %}
其中最重要的地方就是延迟设置位置，担心尺寸重置后还没生效，故做延迟，而不要将延迟放在 `draw` 里面
{% endnote %}

结果：
![](water.jpg)
