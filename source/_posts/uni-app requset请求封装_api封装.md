---
title: uni-app requset请求封装_api封装
tags: 小程序
date: 2022-09-05 00:00:00
category: 前端
---

```javascript
import Request from '../utils/request.js'
import operate from './operate.js'
let request = new Request().http

export const getemoji = function(data) {
  return request({
    url: "",//拼接路径
    method: "GET",
    data: data
  })
}
```
```javascript
export default {
	//接口
	api: ""
}

```
```javascript
import operate from '../common/operate.js'
// vuex 的使用  详情参考官网 https://uniapp.dcloud.io/vue-vuex
import store from '../store/index.js'  

export default class Request {
    http(param) {
        // 请求参数
        var url = param.url,
            method = param.method,
            header = {},
            data = param.data || {},
            hideLoading = param.hideLoading || false;

        //拼接完整请求地址
        var requestUrl = operate.api + url;
		console.log(requestUrl)
       //拼接完整请求地址（根据环境切换）
       // var requestUrl = operate.api() + url;

        //请求方式:GET或POST(POST需配置
        // header: {'content-type' : "application/x-www-form-urlencoded"},)
        if (method) {
            method = method.toUpperCase(); //小写改为大写
            if (method == "GET") {
                header = {
                    'content-type': "application/x-www-form-urlencoded"
                };
            } else {
                header = {
                    'content-type': "application/json"
                };
            }
        }

        //加载圈
        if (!hideLoading) {
            uni.showLoading({
                title: '加载中...'
            });
        }

        // 返回promise
        return new Promise((resolve, reject) => {
            // 请求
            uni.request({
                url: requestUrl,
                data: data,
                method: method,
                header: header,
                success: (res) => {
                    // 判断 请求api 格式是否正确
                    if (res.statusCode && res.statusCode != 200) {
                        uni.showToast({
                            title: "api错误" + res.errMsg,
                            icon: 'none'
                        });
                        return;
                    }
                    // 将结果抛出
                    resolve(res.data)
                },
                //请求失败
                fail: (e) => {
                    uni.showToast({
                        title: "" + e.data,
                        icon: 'none'
                    });
                    resolve(e.data);
                },
                //请求完成
                complete() {
                    //隐藏加载
                    if (!hideLoading) {
                        uni.hideLoading();
                    }
                    resolve();
                    return;
                }
            })
        })
    }
}

```
