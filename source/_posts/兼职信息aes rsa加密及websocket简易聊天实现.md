---
title: 兼职信息aes rsa加密及websocket简易聊天实现
tags: js
date: 2022-11-25 00:00:00
category: 前端
---

### AES加密解密
![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1667463614204-863fd438-7fc3-4eff-b130-8a1673212002.png#clientId=u734cbe78-c3bb-4&from=paste&height=473&id=u9c42a5af&name=image.png&originHeight=473&originWidth=985&originalType=binary&ratio=1&rotation=0&showTitle=false&size=107753&status=done&style=none&taskId=u350cec6f-4f27-4e00-9e62-9063d636e76&title=&width=985)
```javascript
const CryptoJS = require('crypto-js')
// const keyStr = 'encode@3#!8^k.j$'
// const ivStr = 'vector@3#!8^k.j$'
// const txt = '123'

export function encrypt(data, keyS, ivS) {
  // let key = keyS || keyStr
  // let iv = ivS || ivStr
  console.log(data,"data");
  console.log(keyS,"keyS");
  console.log(ivS,"ivS");
 var key = CryptoJS.enc.Utf8.parse(keyS)
 var iv = CryptoJS.enc.Utf8.parse(ivS)
  const src = CryptoJS.enc.Utf8.parse(data)
  const cipher = CryptoJS.AES.encrypt(src, key, {
    iv: iv, // 初始向量
    mode: CryptoJS.mode.CBC, // 加密模式
    padding: CryptoJS.pad.Pkcs7, // 填充方式
  })
  const encrypted = cipher.toString()
  console.log(encrypted,'encrypted');
  return encrypted
}

export function decrypt(data, keyS, ivS) {
  // let key = keyS || keyStr
  // let iv = ivS || ivStr
 var key = CryptoJS.enc.Utf8.parse(keyS)
 var iv = CryptoJS.enc.Utf8.parse(ivS)
  const cipher = CryptoJS.AES.decrypt(data, key, {
    iv: iv,
    mode: CryptoJS.mode.CBC,
    padding: CryptoJS.pad.Pkcs7,
  })
  const decrypted = cipher.toString(CryptoJS.enc.Utf8) // 返回的是加密之前的原始数据->字符串类型
  console.log(cipher,"000");
  return decrypted
}

```
加密:encrypt(数据,密钥,偏移量)
解密:decrypt(数据,密钥,偏移量)
### rsa加密解密
1.npm i wxmp-rsa -S
2.import WxmpRsa from 'wxmp-rsa'
3.实例化rsa
const rsa = new WxmpRsa()
4.定义公钥
const publicKey = `
  -----BEGIN PUBLIC KEY-----
  MIGeMA0GCSqGSIb3DQEBAQUAA4GMADCBiAKBgFnWSUwsmGawhMJ30z6y5li2jcf1
  m7rPMZcwZOS3To8bk3OBaMGhVEc1F8GtJBbc1rn/HCLNL9zrCy21EefJON8tRFcY
  HnpseZSzh+349lIhS+MFw9x4JUddwSPDyxwha929cKzMuVoftu3CJ+kqDBVvxLk7
  iDBzUMqW3Kgehk2TAgMBAAE=
  -----END PUBLIC KEY-----
`
 5.设置公钥
rsa.setPublicKey(publicKey)
6.加密
const cryptStr = rsa.encryptLong(str)
console.log('加密后的结果：', cryptStr)
7.// 定义私钥
const privateKey = `
-----BEGIN RSA PRIVATE KEY-----
MIICWgIBAAKBgFnWSUwsmGawhMJ30z6y5li2jcf1m7rPMZcwZOS3To8bk3OBaMGh
VEc1F8GtJBbc1rn/HCLNL9zrCy21EefJON8tRFcYHnpseZSzh+349lIhS+MFw9x4
JUddwSPDyxwha929cKzMuVoftu3CJ+kqDBVvxLk7iDBzUMqW3Kgehk2TAgMBAAEC
gYBRChPeyk/EOrHX912xLpLKLguh+LY9g1B50ScChzUvtTGDPZaxLQYoogVHKhfn
I9nzuOS5pBzsDX9tAO0hCQzqfHgqRjn+vEgm1Ui+f0E3BVRnhobcJKZpZqlvCBR5
Gu2+zlrY4SeGq3AuQSr/A5FiB5k0RgsvNycDTjqyg7TXGQJBAJoZ8Yr0zakxT1I8
lVqsFbeNPtt8FNG2UgIlIs9RL7aXhw+Y3sWtk/kbaOXafSofu0NcQYx4Km3M3kiP
lcNfTJ8CQQCVPcaRpu+mprRgHS6s76Z668NaFsjX04CUUa0kCrey+Nf/SJJ3BkRH
M7GllZWuI/RSXs/F5N38p5bfkn7QZqaNAkBy3dHJZW8DpgjdYOFnhAxwFK39BwGx
zHhWtv26kWbCcTKwsp+jtB4vunm3k+RmiN6aeGM35L6jt+kdJ0JYLmo7AkBJpRZb
wZj5D8Jqu3vQ8uGgPr9DsYKinkgQ6M0bv/4uXwWXf+Rmv7zpteSv5UTbjfp+uzKk
YO/6QWj+InhZto3xAkAOA0i702dLHm5elLWvht7UEYIDEW1+rYGdbthmJBvT9sZh
VKL954Y9hDzBWepjYsBiJnmIkgeladPnU5025/G/
-----END RSA PRIVATE KEY-----
`
8. 设置私钥
rsa.setPrivateKey(privateKey)
9. 解密
const originalStr = rsa.decryptLong(cryptStr)
console.log('解密后的原始数据：', originalStr)
### WebSocket 是什么？
[WebSocket](http://websocket.org/) 是一种网络通信协议。[RFC6455](https://tools.ietf.org/html/rfc6455) 定义了它的通信标准。
WebSocket 是 HTML5 开始提供的一种在单个 TCP 连接上进行全双工通讯的协议。
### 为什么需要 WebSocket ？
了解计算机网络协议的人，应该都知道：HTTP 协议是一种无状态的、无连接的、单向的应用层协议。它采用了请求/响应模型。通信请求只能由客户端发起，服务端对请求做出应答处理。
这种通信模型有一个弊端：HTTP 协议无法实现服务器主动向客户端发起消息。
这种单向请求的特点，注定了如果服务器有连续的状态变化，客户端要获知就非常麻烦。大多数 Web 应用程序将通过频繁的异步 JavaScript 和 XML（AJAX）请求实现长轮询。轮询的效率低，非常浪费资源（因为必须不停连接，或者 HTTP 连接始终打开）。
### 客户端 API
以下 API 用于创建 WebSocket 对象。
```
var Socket = new WebSocket(url, [protocol] );
```
### WebSocket 的属性
![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1667460339404-c3dd71a2-c2db-4d62-b5f0-0d26d76ac994.png#clientId=u734cbe78-c3bb-4&from=paste&height=395&id=u1750581a&name=image.png&originHeight=395&originWidth=1421&originalType=binary&ratio=1&rotation=0&showTitle=false&size=99705&status=done&style=none&taskId=u741ed1b8-42e7-4d6d-b714-18e4c3a9269&title=&width=1421)
### WebSocket 的事件
![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1667460379331-f48ff246-e082-469a-b50a-a33ead3b9fff.png#clientId=u734cbe78-c3bb-4&from=paste&height=384&id=u6a0a53fe&name=image.png&originHeight=384&originWidth=1431&originalType=binary&ratio=1&rotation=0&showTitle=false&size=60285&status=done&style=none&taskId=uf969632d-02d8-434c-ab31-b0c4cd30db7&title=&width=1431)
### WebSocket 的方法
![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1667460423633-62329e4d-d274-4ce1-80f0-f31b98f27c17.png#clientId=u734cbe78-c3bb-4&from=paste&height=236&id=u3dbde3e6&name=image.png&originHeight=236&originWidth=1418&originalType=binary&ratio=1&rotation=0&showTitle=false&size=25435&status=done&style=none&taskId=u55d6e3df-856e-49f6-819f-cdfe805e1e0&title=&width=1418)
### WebSocket 连接demo
```javascript
// 初始化一个 WebSocket 对象
var ws = new WebSocket('ws://localhost:9998/echo');

// 建立 web socket 连接成功触发事件
ws.onopen = function() {
  // 使用 send() 方法发送数据
  ws.send('发送数据');
  alert('数据发送中...');
};

// 接收服务端数据时触发事件
ws.onmessage = function(evt) {
  var received_msg = evt.data;
  alert('数据已接收...');
};

// 断开 web socket 连接成功触发事件
ws.onclose = function() {
  alert('连接已关闭...');
};

```
### 兼职聊天页面WebSocket 连接步骤
1.全局定义websocket对象
![image.png](https://cdn.nlark.com/yuque/0/2022/png/29366328/1667461811933-5eff9301-1951-4204-85d7-aab306fa67d6.png#clientId=u734cbe78-c3bb-4&from=paste&height=28&id=ub21b3cbf&name=image.png&originHeight=28&originWidth=413&originalType=binary&ratio=1&rotation=0&showTitle=false&size=1724&status=done&style=none&taskId=u2996a451-819b-4d95-a3a2-97262402784&title=&width=413)
2.建立webscoket连接
```javascript
socketTaskRoom = wx.connectSocket({
  url: `wss://mall.wuhanzhuangxiu01.cn/job_ws/chat/${that.$store.state.openid}__${that.jobDetailObj.openid}/?_=${time}&dds=${postData}&rk=${that.key}`,
  success: (res) => {
    console.log(res);
  }
});
```
连接url动态生成，当前用户openid___被连接用户openid + 时间戳 +dds + rk
dds = postdata; rk = key
key = rsa加密（时间戳+三位随机字符串）
```javascript
urlData = `user_id=${that.$store.state.openid}&username=${that.$store.state.user_name}&chat_with_id=${that.jobDetailObj.openid}&chat_with_name=${that.jobDetailObj.username}&chat_job_id=${that.jobDetailObj.id}&chat_job_title=${that.jobDetailObj.title}&appid=${that.$store.state.appid}&avatar=${that.$store.state.user_img}&chat_with_avatar=${that.jobDetailObj.headimg}`
postData = encrypt(urlData,time+randomStr,this.iv)
```
iv = MD5加密（时间戳）.substr(8,16)
```javascript
socketTaskRoom.onOpen(function (res) {
					  console.log('WebSocket连接已打开！');
					  socketOpen = true
					});
					socketTaskRoom.onError(function (res) {
					  console.log('WebSocket连接打开失败，请检查！');
					  socketOpen = false
					});
					socketTaskRoom.onMessage(function(res) {
						console.log('收到服务器内容：' + res.data);
						that.getSocketMsg(res.data); // 监听到有新服务器消息
						that.talkList.push(JSON.parse(res.data))
					
					})
					socketTaskRoom.onClose(function (res) {
					  console.log('WebSocket 已关闭！');
					});
```
所有数据处理完成后，连接成功，通过onopen方法监听到连接成功消息，
通过onMessage收到服务端推送的消息，拿到消息后json parse push进聊天列表（此时消息都是新的聊天内容）
本地通过send方法发送消息
```javascript
let obj = {send_user_id: that.$store.state.openid, send_username: that.$store.state.user_name, send_time: "2022-10-13 11:40:00", send_message: "我是user1", chat_with_id: that.oid, chat_with_name: that.jobDetailObj.username,chat_job_id:that.jobDetailObj.id,chat_job_title:that.jobDetailObj.title,appid:that.$store.state.appid,avatar:that.$store.state.user_img,chat_with_avatar:that.jobDetailObj.headimg}
socketTaskRoom.send({
						data: JSON.stringify(obj),
						success: (res) => {
							console.log(res);
						}
					});
```
消息发送成功后，onMessage可以监听到服务端返回的消息列表，即你此时发送成功的消息。
```javascript
// 发送信息
			send(){
				if(!this.content){
					uni.showToast({
						title:'请输入有效的内容',
						icon:'none'
					})
					return;
				}
				uni.showLoading({
					title:'正在发送'
				})
				setTimeout(()=>{
					uni.hideLoading();
					this.sendMessage()
					this.$nextTick(()=>{
						// 清空内容框中的内容
						this.content = '';
						uni.pageScrollTo({
						    scrollTop: 999999,	// 设置一个超大值，以保证滚动条滚动到底部
						    duration: 0
						});
					})
				},500);
			},
```
### 聊天历史记录获取
```javascript
getHistory(){
				let that = this
				console.log(that.jobDetailObj,"1111111");
				uni.request({
					url:`https://mall.wuhanzhuangxiu01.cn/job_chat/ch`,
					data:{
						"user_id":that.$store.state.openid,
						"chat_with_id":that.type==2?that.oid:that.jobDetailObj.openid,
						"appid":that.$store.state.appid
					},
					method:"POST",
					success: (res) => {
						let jsonData = decrypt(res.data.data,"2031618372682131","9329859705602774")
						// console.log(res.data.data);
						console.log(jsonData,"解密后");
						that.hideLoadTips(true);
						that.talkList = JSON.parse(jsonData)
						
					}
				})
			},
```
### 聊天列表界面websocket
```javascript
getChatList(){
				let that = this
				socketTaskList = wx.connectSocket({
				  url: 'wss://mall.wuhanzhuangxiu01.cn/job_ws/chatList/'
				});
				socketTaskList.onOpen(function (res) {
				  console.log('WebSocket连接已打开！');
				  that.btn()
				  socketOpen = true
				});
				socketTaskList.onError(function (res) {
				  console.log('WebSocket连接打开失败，请检查！');
				  socketOpen = false
				});
				socketTaskList.onMessage(function(res) {
					console.log('收到服务器内容：' + res.data);
					let data = decrypt(res.data,'1189789282863375','5671714989170848')
					console.log(JSON.parse(data));
					that.list = JSON.parse(data)
				
				})
				socketTaskList.onClose(function (res) {
				  console.log('WebSocket 已关闭！');
				});
			},
```
页面跳转时断开连接
socketTaskList.close()
### websocket重连机制
```javascript
checkOpenSocket() {
				socketTaskRoom.send({
					data: 'ping',
					success: res => {
						console.log(res);
						return;
					},
					fail: err => {
						// 未连接打开websocket连接
						this.lianJie()
					}
				});
			},
// 检测心跳reset  在监听到服务端消息时调用
			reset() {    
				clearInterval(this.timeoutObj);
				this.start(); // 启动心跳
			},
			// 启动心跳 start
			start() {
				let that = this
				this.timeoutObj = setInterval(function() {
					socketTaskRoom.send({
						data: 'ping',
						success: res => {
							console.log(res);
							
						},
						fail: err => {
							console.log('连接失败重新连接....');
							// that.addKey();
						}
					});
				}, this.timeout);
			},
```

