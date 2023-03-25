---
title: 从promise到async
tags: js
date: 2022-08-14 00:00:00
category: 前端
---

JS是一项单线程语言，所有的任务都会在一条线程上执行，但是一些高消耗的操作会堵塞线程。因此JS有同步模式和异步模式。
首先宏任务是由node&浏览器主动发起的，而微任务是JS本身发起的
宏任务：setTimeout，setInterval，script（整体代码块）
微任务：promise.the……,nexttick
# 宏任务与微任务在eventloop中的执行顺序
![](https://cdn.nlark.com/yuque/0/2022/webp/29366322/1660275066442-6c2818dc-88df-447f-a0bb-6a293e97e297.webp#clientId=u49dad62b-8b4b-4&from=paste&id=u6e30d84e&originHeight=529&originWidth=491&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u8e710615-c6b0-4478-b0d8-61f2fb6894b&title=)
简单来说第一个宏任务执行，如果微任务队列存在，则将所有的微任务执行完再继续执行下一个宏任务，直到所有的宏任务执行完毕。
# promise
promise有三种状态：pending(等待态)，fulfiled(成功态)，rejected(失败态)；状态一旦改变，就不会再变。创造promise实例后，它会立即执行。
在异步任务中，最常见的就是请求嵌套，但小程序的requset请求。
```javascript
uni.requset({
  url:'',
  success(){
    uni.requset({
      url:'',
      success(){
        uni.requset({
          url:'',
          success(){
            
          }
        })
      }
    })
  }
})
```
这样代码易读性就很烂，就算拆出来单独调用也会增加重复的代码量。（但无所谓）
那么promise能干什么呢
promise是用来解决两个问题的：

- 回调地狱，代码难以维护， 常常第一个的函数的输出是第二个函数的输入这种现象
- promise可以支持多个并发的请求，获取并发请求中的数据

## Promise的使用
Promise是一个构造函数，自己身上有all、reject、resolve这几个眼熟的方法，原型上有then、catch等同样很眼熟的方法。
```javascript
let p = new Promise((resolve, reject) => {
    //做一些异步操作
    setTimeout(() => {
        console.log('执行完成');
        resolve('我是成功！！');
    }, 2000);
});
```
_Promise {<pending>}_

   1. __proto__: Promise
   2. _[[PromiseState]]_: "pending"
   3. _[[PromiseResult]]_

在promise的原型上，我们可以看见他有以下的结构，以及现在的状态，promiseState。

1. catch: _ƒ catch()_
2. constructor: _ƒ Promise()_
3. finally: _ƒ finally()_
4. then: _ƒ then()_

_但我们只需要看_constructor上的东西就行了

1. **all: **_**ƒ all()**_
### 谁跑的慢，以谁为准执行回调。all接收一个数组参数，里面的值最终都算返回Promise对象

2. allSettled: _ƒ allSettled()_
3. any: _ƒ any()_
4. arguments: (...)
5. caller: (...)
6. length: 1
7. name: "Promise"
8. nv_length: (...)
9. prototype: Promise {Symbol(Symbol.toStringTag): "Promise", constructor: _ƒ_, then: _ƒ_, catch: _ƒ_, finally: _ƒ_}
10. **race: **_**ƒ race()**_
### 谁跑的快，以谁为准执行回调

11. **reject: **_**ƒ reject()**_

把Promise的状态置为rejected，这样我们在then中就能捕捉到，然后执行“失败”情况的回调。then和catch都能捕捉到失败的回调，只不过catch大部分时候是抛出异常让后面的代码继续执行的情况才会使用。

12. **resolve: **_**ƒ resolve()**_
13. Symbol(Symbol.hasInstance): _ƒ value(e)_
14. Symbol(Symbol.species): (...)
15. get Symbol(Symbol.species): _ƒ [Symbol.species]()_

then的链式调用
```javascript
let p = new Promise(function(){
    resolve('我是成功');
})
p.then((data) => {console.log(data);},(err) => {});
p.then((data) => {console.log(data);},(err) => {});
p.then((data) => {console.log(data);},(err) => {});
```
_扩展写法_
```javascript
  then(onFulFilled, onRejected) {
    if (this.status === 'resolved') {
      onFulFilled(this.value);
    }
    if (this.status === 'rejected') {
      onRejected(this.reason);
    }
    // 当前既没有完成 也没有失败
    if (this.status === 'pending') {
      // 存放成功的回调
      this.onResolvedCallbacks.push(() => {
        onFulFilled(this.value);
      });
      // 存放失败的回调
      this.onRejectedCallbacks.push(() => {
        onRejected(this.reason);
      });
    }
  }
```
那么在知道了这些情况之后
那么我们就可以把请求变成微任务来调用以达成我们需要的异步变同步。
```javascript
new Promise((resolve,reject)=>{
    uni.request({
      url: ,
      data:,
      method: ,
      header:header,
      complete:(res)=>{
          console.log(`耗时${Date.now() - timeStart}`);
          if(res.statusCode ==200){//请求成功
            resolve(res.data)
          }else{
            reject(res);
          }
      }
    })

//暴露出去
```
```javascript
//如果在页面中需要发起多个需要同步的请求，那么
Promise.all([
  api.get('param1'),
  api.get('param2')
]).then(result => {
  console.log(result)
}).catch(e => {
  console.log(e)
})
```
# async await
async 函数是 Generator 函数的语法糖,但暂时可以先不管Generator 是什么。
```javascript
function req(){
  uni.request(...)
}
```
Promise 的方式虽然解决了 callback hell，但是这种方式充满了 Promise的 then() 方法，如果处理流程复杂的话，整段代码将充满 then。语义化不明显，代码流程不能很好的表示执行流程。（主要是写起来麻烦）
```javascript
const delay = timeout => new Promise(resolve=> setTimeout(resolve, timeout));
async function f(){
    await delay(1000);
    await delay(2000);
    await delay(3000);
    return 'done';
}

f().then(v => console.log(v)); // 等待6s后才输出 'done'
```
我们来模拟一下请求，那么在三个请求发完之后，我们才会继续执行，来达成异步。用法就是这么简单，但是如何来处理错误呢。
```javascript
const delay = timeout => new Promise(resolve=> setTimeout(resolve, timeout));
async function f(){
    await delay(1000);
    await delay(2000);
    await Promise.reject('error');
    await delay(3000);
    return 'done';
}

f().then(v => console.log(v)); // 等待3s后才输出 'error' 

```
当 async 函数中只要一个 await 出现 reject 状态，则后面的 await 都不会被执行。
```javascript
const delay = timeout => new Promise(resolve=> setTimeout(resolve, timeout));
async function f(){
    await delay(1000);
    await delay(2000);
    try {
        await Promise.reject('error')
    } catch (error) {
        console.log(error);
    }
    await delay(3000);
    return 'done';
}

f().then(v => console.log(v)); // 等待6s后才输出 'error' 

```
