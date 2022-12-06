<template>
	<view class="container">
		<!-- <view v-if="status === 'loading'" class="loading">
			<text>Loading</text>
		</view> -->
		<!-- <view v-else class="main"> -->
		<view class="main">
			<uni-swipe-action>
			    <uni-swipe-action-item 
					v-for="(mode, index) in plans"
					:key="index+mode.name"
					:right-options="options"  
					@click="onClick" 
					@change="swipeChange($event, index)"
				>
					<view class="item">
						<view class="title">{{mode.name}}</view>
						<view class="detail">{{mode.keyPts}}</view>
					</view>
			    </uni-swipe-action-item>
			</uni-swipe-action>
		</view>
		<view class="btn">
			<image class="icon" src="/static/add.png" mode="widthFix"></image>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// status: "loading",
				plans: [],
				options:[
					{
						text: '编辑',
						style: {
							backgroundColor: '#007aff'
						}
					}, {
						text: '删除',
						style: {
							backgroundColor: '#dd524d'
						}
					}
				]
			}
		},
		methods: {
			onClick(e){
				console.log(e)
				console.log('点击了'+(e.position === 'left' ? '左侧' : '右侧') + e.content.text + '按钮')
			},
			swipeChange(e,index){
				console.log('当前状态：'+ e +'，下标：' + index)
			},
			toEdit() {
				uni.navigateTo({
					url: "/pages/edit/edit"
				})
			}
		}, 
		onLoad() {
			this.plans = getApp().globalData.plans
			this.status = "done"
		}
	}
</script>

<style>
	.container {
		padding-left: 8px;
		height: 100%;
		display: flex;
		flex-direction: column;
	}
	.loading {
		display: flex;
		height: 100%;
		flex-direction: column;
		justify-content: center;
		align-items: center;
	}
	.main {
		
	}
	.item {
		padding: 4px 0px;
	}
	.title {
		font-size: 24px;
	}
	.detail {
		font-size: 12px;
		font-weight: 100;
	}
	.btn {
		width: 64px;
		align-self: center;
		margin-top: 24px;
	}
	.icon {
		width: 100%;
		max-height: 100%;
	}
</style>
