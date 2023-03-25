---
title: uni-app搜索页面（本地历史记录缓存）
tags: 小程序
date: 2022-09-03 00:00:00
category: 前端
---

```vue
<template>
	<view class="container">
		<view class="search-btn" @click="focus()">
			<uni-search-bar @confirm="search" bgColor="#EDEDED" radius="20" cancelButton="none" placeholder="输入模板关键字"
				v-model="searchValue"> </uni-search-bar>
		</view>
		<view class="history">
			<view class="title">
				<text style="font-weight: 900;">历史记录</text>
				<uni-icons type="trash" size="20" @click="clean()"></uni-icons>
			</view>
			<view class="example-body">
				<view class="tag-view" v-for="(item,index) in list" @longpress="deleted(index)">
					<uni-tag :circle="true" :text="item" @click="tohistory(item)"
						custom-style="background-color: #F4F4F4; border-color:#F4F4F4; color: #2f2f2f;" />
				</view>
			</view>
		</view>
		<uni-popup ref="popup" type="dialog">
			<uni-popup-dialog mode="base" message="成功消息" title="确认删除？" :duration="2000" :before-close="true"
				@close="close" @confirm="confirm"></uni-popup-dialog>
		</uni-popup>
		<uni-popup ref="clean" type="dialog">
			<uni-popup-dialog mode="base" message="成功消息" title="删除全部历史记录？" :duration="2000" :before-close="true"
				@close="close" @confirm="clearall"></uni-popup-dialog>
		</uni-popup>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				searchValue: '',
				list: [],
				index: ''
			}
		},
		methods: {
			// 去重
			removeDuplicate(arr) {
				const newArr = []
				arr.forEach(item => {
					if (newArr.indexOf(item) === -1) {
						newArr.push(item)
					}
				})
				return newArr // 返回一个新数组
			},
			search(res) {

				this.list.push(res.value)
				this.list = this.removeDuplicate(this.list)
				uni.setStorageSync('history', this.list)

				uni.navigateTo({
					url:'../calorie_category/calorie_category?word='+ res.value
				})
				this.searchValue = ''
			},
			deleted(index) {
				this.$refs.popup.open()
				this.index = index
			},
			clean() {
				this.$refs.clean.open()
			},
			clearall() {
				this.list = []
				uni.setStorageSync('history', this.list)
				this.$refs.clean.close()
			},
			confirm() {
				this.list.splice(this.index, 1)
				this.$refs.popup.close()
				this.index = ''
				uni.setStorageSync('history', this.list)
			},
			close() {
				this.$refs.popup.close()
				this.$refs.clean.close()
			},
			tohistory(item) {
				uni.navigateTo({
					url:'../calorie_category/calorie_category?word='+ item
				})
			},
			focus() {

			}
		},
		onLoad() {
			if (uni.getStorageSync('history')) {
				this.list = uni.getStorageSync('history')
			}
		}
	}
</script>

<style>
	.container {
		height: 100%;
		background-color: white;
	}

	.history {
		margin: 20rpx;
	}

	.title {
		display: flex;
		justify-content: space-between;
		margin: 20rpx 0;
	}

	.example-body {
		display: flex;
		flex-direction: row;
		justify-content: flex-start;
		align-items: flex-end;
		flex-wrap: wrap;
	}

	.tag-view {
		margin-right: 10px;
	}

</style>

```
