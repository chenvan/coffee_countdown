<template>
	<view class="main">
		<uni-swipe-action>
			<uni-swipe-action-item 
				v-for="(mode, index) in plans"
				:key="index"
				:right-options="options"  
				@click="onDel(index)" 
			>
				<view class="item" @click="toEdit(index)">
					<view class="title">{{mode.name}}</view>
					<view class="detail">{{mode.keyPts}}</view>
				</view>
			</uni-swipe-action-item>
		</uni-swipe-action>
		<view class="ctrlZone" >
			<view @click="addPlan">
				<image class="icon" src="/static/add.png" mode="widthFix"></image>
			</view>
		</view>
		<uni-popup ref="errMsg" type="center">
			<uni-popup-dialog content="保存方案失败"></uni-popup-dialog>
		</uni-popup>
		<uni-popup ref="delConfirm" type="center">
			<uni-popup-dialog content="确认删除" @confirm="delPlan"></uni-popup-dialog>
		</uni-popup>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				plans: [],
				delId: undefined,
				options:[{
					text: '删除',
					style: {
						backgroundColor: '#dd524d'
					}
				}]
			}
		},
		methods: {
			onDel(id){
				this.delId = id
				this.$refs.delConfirm.open()
			},
			toEdit(id) {
				uni.navigateTo({
					url: `/pages/edit/edit?id=${id}`
				})
			},
			addPlan() {
				uni.navigateTo({
					url: `/pages/edit/edit`
				})
			},
			async delPlan() {
				let lastSelect = getApp().globalData.lastSelect
				
				if(lastSelect >= this.delId) {
					getApp().globalData.lastSelect = lastSelect === this.delId ? 0 : lastSelect - 1
				}
				
				let shadowPlans = [...this.plans]
				shadowPlans.splice(this.delId, 1)
				this.plans = shadowPlans

				getApp().globalData.isPlanChange = true
				// 在 Plan 进行删除后, 需要更新 globalData
				getApp().globalData.plans = this.plans
				
				// save
				await this.savePlans()
				
			},
			async savePlans(newPlans) {
				// 存 this.plans, 因此使用前必需先更新 plans
				
				// 保存不会对操作影响?
				try {
					await uni.setStorage({
						key: "plans",
						data: this.plans
					})
				} catch(err) {
					this.$refs.errMsg.open()
				}
			}
		}, 
		onLoad() {
			this.plans = getApp().globalData.plans
		},
		async onShow() {
			if(getApp().globalData.isEditChange) {
				// Edit页面对方案进行了新增或者修改
				this.plans = getApp().globalData.plans
				await this.savePlans()
				getApp().globalData.isPlanChange = true
				getApp().globalData.isEditChange = false
			}
		}
	}
</script>

<style>
	.main {
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
	.ctrlZone {
		position: fixed;
		bottom: 32px;
		right:0;
		width: 100%;
		display: flex;
		justify-content: space-around;
	}
	.icon {
		width: 64px;
		height: 64px;
	}
</style>
