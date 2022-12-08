<template>
	<view class="container">
		<view class="main">
			<uni-swipe-action>
			    <uni-swipe-action-item 
					v-for="(mode, index) in plans"
					:key="index+mode.name"
					:right-options="options"  
					@click="onClick($event, index)" 
				>
					<view class="item">
						<view class="title">{{mode.name}}</view>
						<view class="detail">{{mode.keyPts}}</view>
					</view>
			    </uni-swipe-action-item>
			</uni-swipe-action>
		</view>
		<view class="btn" @click="addPlan">
			<image class="icon" src="/static/add.png" mode="widthFix"></image>
		</view>
		<uni-popup ref="loading" type="center">
			<text>Loading</text>
		</uni-popup>
		<uni-popup ref="del" type="center">
			<uni-popup-dialog title="确认删除" @confirm="del"></uni-popup-dialog>
		</uni-popup>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// status: "loading",
				plans: [],
				delId: undefined,
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
			onClick(e, id){
				if(e.content.text === "编辑") {
					uni.navigateTo({
						url: `/pages/edit/edit?id=${id}`
					})
				} else {
					this.delId = id
					this.$refs.del.open()
				}
			},
			addPlan() {
				uni.navigateTo({
					url: `/pages/edit/edit`
				})
			},
			async del() {
				let lastSelect = getApp().globalData.lastSelect
				
				if(lastSelect >= this.delId) {
					getApp().globalData.lastSelect = lastSelect === this.delId ? 0 : lastSelect - 1
				}
				
				let temp = [...this.plans]
				temp.splice(this.delId, 1)
				this.plans = temp

				getApp().globalData.isChange = true
				getApp().globalData.plans = this.plans
				
				// save
				await this.save()
				
			},
			async save() {
				this.$refs.loading.open()
				await uni.setStorage({
					key: "plans",
					data: this.plans
				})
				this.$refs.loading.close()
			}
		}, 
		onLoad() {
			this.plans = getApp().globalData.plans
		},
		async onShow() {
			if(getApp().globalData.isChange) {
				await this.save()
				this.plans = getApp().globalData.plans
				getApp().globalData.isChange = false
			}
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
