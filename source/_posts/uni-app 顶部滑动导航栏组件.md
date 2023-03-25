---
title: uni-app 顶部滑动导航栏组件
tags: 小程序
date: 2022-09-11 00:00:00
category: 前端
---

```vue
<template>
    <view class="uni-tab-bar">
        <scroll-view class="uni-swiper-tab" scroll-x="true">
            <block v-for="(tab,index) in tabBars" :style="scrollStyle">
                <view class="swiper-tab-list" :class="{'active' : tabIndex==index}" @tap="tabtap(index)"
                    :style="scrollItemStyle">
                    {{tab.name}} {{tab.num?tab.num:""}}
                    <view class="swiper-tab-line"></view>
                </view>
            </block>
        </scroll-view>
    </view>
</template>

<script>
    export default {
        props: {
            tabBars: Array,
            tabIndex: Number,
            scrollStyle: {
                type: String,
                default: ""
            },
            scrollItemStyle: {
                type: String,
                default: ""
            }
        },
        methods: {
            //点击切换导航
            tabtap(index) {
                // this.tabIndex = index;
                this.$emit('tabtap', index)
            }
        }
    }
</script>　

<style>

</style>
```
```vue
<template>
	    <view class="content">
		 <swiperTabHead :tabBars="tabBars" :tabIndex="tabIndex" @tabtap="tabtap"></swiperTabHead>
		<swiper class="swiper-content" :style="{height:swiperheight+'rpx'}" :current="tabIndex" @change="swiperChange">
			  <swiper-item class="itemswiper" :key="index2" v-for="(newItem ,index2) in tabBars">
				  <view class="viewat">
					{{newItem.id}} {{newItem.name}}
				</view>
			</swiper-item>
			  </swiper>
	</view>
</template>

<script>
	import swiperTabHead from "../../components/scrollbar.vue"
	export default {
		components: {
			swiperTabHead
		},
		data() {
			return {
				swiperheight: 0,
				tabIndex: 0, // 选中的
				tabBars: [{
						name: "关注",
						id: "guanzhu"
					},
					{
						name: "推荐",
						id: "tuijian"
					},
					{
						name: "体育",
						id: "tiyu"
					},
					{
						name: "热点咨询",
						id: "redian"
					},
					{
						name: "财经",
						id: "caijing"
					},
					{
						name: "娱乐",
						id: "yule"
					},
					{
						name: "社会",
						id: "yule"
					},
					{
						name: "自然",
						id: "yule"
					},
					{
						name: "币圈",
						id: "yule"
					},
					{
						name: "国家体育",
						id: "yule"
					},
				]
			}
		},
		onLoad() {
			var _this = this
			uni.getSystemInfo({
				success(res) {
					console.log("屏幕高度 HH = " + res.windowHeight)
					console.log("屏幕高度 HH222 = " + res.windowHeight * 2)
					_this.swiperheight = res.windowHeight * 2 - 68
				}
			})
		},
		methods: {
			//接受子组件传过来的值点击切换导航
			tabtap(index) {
				var _this = this
				_this.tabIndex = index;
			},
			// 滑动切换导航
			swiperChange: function(e) {
				var _this = this
				_this.tabIndex = e.detail.current
			}
		}
	}
</script>

<style>
</style>

```
