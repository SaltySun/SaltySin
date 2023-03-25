---
title: 微信小程序 webview 与 h5 的通信
tags: js
date: 2022-12-22 00:00:00
category: 前端
---

参考资料
[Uniapp 内嵌H5跳转内嵌小程序页面](https://blog.csdn.net/m0_37792384/article/details/115952370)
[uniapp h5项目点击跳转小程序，h5传参到小程序](https://blog.csdn.net/rxh13543515695/article/details/124980667)
[微信小程序与h5通过web-view传值](https://blog.csdn.net/m0_56712653/article/details/117741481)
[[填坑手册]小程序web-view组件实战与踩坑](https://developers.weixin.qq.com/community/develop/article/doc/000a0ce2858618976b0a2df3b5bc13)

首先因为微信小程序无法打开本地资源管理器。所以都是通过微信的`wx.chooseMessageFile`进行在本地聊天记录中选取文件。（这种方法也是目前大部分小程序采取上传文件的方式优点是：速度快、兼容性好。缺点：用户需要提前把文件发送给自己聊天列表中的随便一个人）
`wx.chooseMessageFile`代码实现

```
wx.chooseMessageFile({
  count: 10,
  type: 'file', //	选择了除图片和视频的文件
  success (res) {
    // tempFilePath可以作为 img 标签的 src 属性显示图片
    const tempFilePaths = res.tempFiles
  }
})
```

现在需要直接通过微信小程序去访问本地的资源管理器。我的解决思路的是，通过微信小程序使用webview去调用一个h5页面。在通过h5页面去调用本地资源。选取后将资源上传服务器，在返回到小程序。这里比较困难的是小程序向webview传值以及webview跳回小程序，以及返回参数到小程序。

这里首先需要写一个h5的页面进行上传文件,这里需要引入小程序的一个sdk,uniapp使用 `npm install --save-dev weixin-js-sdk`。然后在main.js里进行全局引入`let jweixin = require('jweixin-module')` `Vue.prototype.$wx = jweixin`。

```
<template>
	<view class="content">
		<view class="add-btn">
			<text class="text" @click="openFile">添加附件</text>
		</view>
	</view>
</template>

<script>
	import wx from '@/static/activity_sdk.js';
	export default {
		data() {
			return {
				title: 'Hello',
				id: '',
				token: ''
			}
		},
		methods: {
			// 打开文件选择器
			openFile() {
				let that = this
				uni.chooseFile({
					count: 1, //默认100
					extension: ['.mp3', '.m4a'],
					success: (res) => {
						console.log(res);
						if (res.tempFiles[0].size / 1024 / 1024 > 20) {
							that.$refs.uToast.show({
								title: '附件大小不能超过20M',
								type: 'warning',
							})
							return;
						}
						var params = {
							path: res.tempFilePaths[0],
							name: res.tempFiles[0].name,
							orderid: 0,
						}
						that.uploadFile(params);
					}
				});
			},

			uploadFile(params, noRefetch) {
				uni.showLoading({
					title: '上传中...',
				});
				var that = this;
				var url = 'https://dub.wuhanzhuangxiu01.cn/uploadMusic';
				var name = params.name ? params.name : '';
				var key = params.key ? params.key : '';
				var ii = params.ii ? params.ii : 0;
				var orderid = params.orderid ? params.orderid : 0;
				var id = that.id ? that.id : 0;
				uni.uploadFile({
					url: url,
					filePath: params.path,
					fileType: 'audio',
					name: 'file',
					header: {
						'token': that.token,
					},
					formData: {
						'path': params.path,
						'name': name,
						'key': key,
						'ii': ii,
						'uid': id,
						'orderid': orderid
					},
					success: function(res) {
						var data = JSON.parse(res.data);
						uni.hideLoading();
						that.$wx.miniProgram.postMessage({
							data: {
								name: data,
							}
						});
						that.$wx.miniProgram.navigateBack({
							delta: 1
						})
					},
					fail: function(res) {
						console.log('fail:', res);
						uni.hideLoading();
						uni.showLoading({
							title: '上传失败',
							icon: 'error'
						});
						that.$wx.miniProgram.navigateBack({
							delta: 1
						})
					},
				})
			},
		},
		onLoad(e) {
			console.log('h5页面', e);
			this.id = e.id
			this.token = e.token
		}
	}
</script>

<style>
	.add-btn {
		width: 100%;
		height: 100vh;
		display: flex;
		justify-content: center;
		align-items: center;

	}

	.add-btn text {
		display: block;
		text-align: center;
		line-height: 100rpx;
		width: 80%;
		height: 100rpx;
		background-color: cadetblue;
		border-radius: 20rpx;
		/* margin: 0 auto; */
	}
</style>
```

这里从webview的网页跳回小程序有个问题。就是只能用`navigateBack`进行返回。原因不明。文档上说的是navigateTo、navigateBack、switchTab、reLaunch、redirectTo都能使用，但是在我的环境下只能使用`navigateBack`进行返回。传值我是通过webview的bindmessage属性进行传值。首先在webview定义好属性写好回调。在h5页面，使用wx.miniProgram.postMessage来进行传值。传的值会在 bindmessage定义好的函数里形参里。
因为之前我是使用 `navigateBack`进行返回的。所以返回的值一直在webview页面。我需要传值给我的返回的这个界面。我在app.js里面定义一个属性进行页面之间的通信。在webview页面接受传回来的值，存放在app.js上面的属性。在跳转的页面进行拿取传过来的值。就能完成从webview传值给微信小程序。

微信小程序webview示例代码：

```
<!-- wxml部分 -->
<web-view src="{{'https://dub.wuhanzhuangxiu01.cn/uploading/upload/'+'#/?id='+id+'&token='+token}}" bindload="load" bindmessage="msgHandler"></web-view>
<!-- js部分 -->
let app = getApp()
Page({

  /**
   * 页面的初始数据
   */
  data: {
    id:"",
    token:""
  },
  //可以捕获到url的监听事件函数
  load: function (e) {
    // 获取url
    const src = e.detail.src;
    console.log(src);
  },
  <!-- 通信函数 -->
  msgHandler: function (e) { //(h5像小程序传递参数）
    console.log('传回的值',e.detail.data[0]) //获取到来自也页面的数据
    app.muName = e.detail.data[0]

  },
  /**
   * 生命周期函数--监听页面加载
   */
  onLoad(options) {
    this.setData({
      id:options.id,
      token:options.token
    })
  },
})
```

这部分代码中的wxml部分，src部分不要问我怎么写这么复杂，我也不想写这么复杂。我试了无数次，只有这么写，才能带参数，访问这个h5页面。之前我是先将url合并之后一块给src,但是这个h5会在链接自动生成`#`在路径里。然后微信这边会自动将#号过滤掉。这个#号是因为路由器的两种工作模式之一的hash模式造成的。

> hash ——即地址栏URL中的#符号。 hash 虽然出现URL中，但不会被包含在HTTP请求中，对后端完全没有影响，因此改变hash不会重新加载页面。


> history ——利用了HTML5 History Interface 中新增的pushState() 和replaceState() 方法。需要特定浏览器支持 history模式，会出现404 的情况，需要后台配置。


> hash模式下，仅hash符号之前的内容会被包含在请求中，如 `https://www.shopwind.net`, 因此对于后端来说，即使没有做到对路由的全覆盖，也不会返回404错误；


> history模式下，前端的url必须和实际向后端发起请求的url 一致，如`https://www.shopwind.net/a/` 。如果后端缺少对/a 的路由处理，将返回404错误


详解可见 [Uniapp发布为H5版本时如何隐藏访问路径的#符号](https://blog.csdn.net/qq_37109041/article/details/118962254)

因为我这边后端是别人搭好的，如果要改，可能有连锁反应，所以我最后，在不断百度，不断百度，不断百度。最后在犄角旮旯找到一篇文章，在文章的评论里找到一条评论，让我在webview里直接将`#/`以字符串的形式加进去，能直接访问。于是就有了这部分内容。
