---
title: 小程序echarts动态渲染过程
tags: 小程序
date: 2022-08-11 00:00:00
category: 前端
---

1.请求的数据先行（request保存数据没什么好说的）
2.在最外层定义charts实例为空，ec绑定data外方法
```html
<ec-canvas id="mychart-dom-pie" class="ech1" canvas-id="mychart-pie" ec="{{ ec }}"></ec-canvas>
```
记得给高度
```javascript
var Charts1 = null;
const app = getApp();
Page({
  data{
    ec:{
        onInit:initChart1
      }
  }
})
function initChart1(canvas, width, height, dpr) {

  const chart = echarts.init(canvas, null, {
    width: width,
    height: height,
    devicePixelRatio: dpr // new
  });
  canvas.setChart(chart);
  return chart;

```
3.定义一个方法来获取return需要渲染的数据
```javascript
 getOption1(){
    var option1 = {
      backgroundColor: "#ffffff",
      title: {
        subtext: '下拉刷新获取最新数据'
      },
      series: [{
        label: {
          normal: {
            fontSize: 14
          }
        },
        type: 'pie',
        center: ['50%', '50%'],
        radius: ['35%', '55%'],
        data: app.globalData.pie,
        emphasis: {
          itemStyle: {
            shadowBlur: 10,
            shadowOffsetX: 0,
            shadowColor: 'rgba(0, 0, 0, 0.5)'
          }
        }
      }]
    };
    return option1
  }
```
4.接下来初始化数据，初始化数据和第二次渲染方法分开写，在进入页面时在onload中获取echarts的id
```javascript
this.echartsComponnet1 = this.selectComponent('#mychart-dom-pie');
```
```javascript
  init_echarts1: function () {
    this.echartsComponnet1.init((canvas, width, height) => {
      // 初始化图表
      const Chart = echarts.init(canvas, null, {
        width: width,
        height: height
      });
      this.setOption1(Chart)
      // 注意这里一定要返回 chart 实例，否则会影响事件处理等
      return Chart;
    });
  }
```
```javascript
setOption1: function (Chart1) {
    Chart1.clear(); // 清除
    Chart1.setOption(this.getOption1()); //获取新数据
 }
```
5.动态加载时调用二次加载图标的方法就行
