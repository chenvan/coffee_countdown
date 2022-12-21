<template>
	<view class="main">
		<view 
			class="planZone"
			@longpress="toggleMode"
		>
			<PlanItem
				v-for="(plan, index) in plans"
				:key="index"
				:id="index"
				:item="plan"
				:isDelMode="isDelMode"
				:isSelected="!isDelMode ? null : delIdMap[index]"
				@select="toggleDelIdState"
			></PlanItem>
		</view>
		<view class="ctrlZone" >
			<view v-if="isDelMode" @click="toDelConfirm"> 
				<image class="iconlite" src="/static/del.png" mode="widthFix"></image>
			</view>
			<view v-if="isDelMode" @click="selectAll">
				<image class="iconlite" src="/static/sel_all.png" mode="widthFix"></image>
			</view>
			<view v-if="!isDelMode" @click="addPlan">
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
				isDelMode: false,
				// delId: undefined,
				delIdMap: [],
			}
		},
		methods: {
			toDelConfirm(){
				this.$refs.delConfirm.open()
			},
			toEdit(id) {
				uni.navigateTo({
					url: `/pages/edit/edit?id=${id}`
				})
			},
			toggleDelIdState(id) {
				this.delIdMap[id] = !this.delIdMap[id]
			},
			addPlan() {
				uni.navigateTo({
					url: `/pages/edit/edit`
				})
			},
			toggleMode() {
				if(!this.isDelMode) {
					this.isDelMode = true
					this.delIdMap = Array(this.plans.length).fill(false)
				} else {
					this.isDelMode = false
					this.delIdMap = []
				}
			},
			selectAll() {
				if(this.delIdMap.every(value => value === true)) {
					this.delIdMap = Array(this.plans.length).fill(false)
				} else {
					this.delIdMap = Array(this.plans.length).fill(true)
				}
			},
			async delPlan() {
				let lastSelect = getApp().globalData.lastSelect
				
				if(this.delIdMap[lastSelect]){
					lastSelect = 0
				} else {
					let temp = this.delIdMap.slice(0, lastSelect).filter(state => state).length
					lastSelect -= temp
					// console.log(temp, lastSelect - temp)
				}
				
				
				getApp().globalData.lastSelect = lastSelect

				this.plans = this.plans.filter((plan, index) => !this.delIdMap[index])

				this.delIdMap = Array(this.plans.length).fill(false)
				
				getApp().globalData.isPlanChange = true
				// 在 Plan 进行删除后, 需要更新 globalData
				getApp().globalData.plans = this.plans
				
				// save
				await Promise.all([
					this.savePlans(),
					this.saveLastSelect(lastSelect)
				])
				
			},
			async savePlans() {
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
			},
			async saveLastSelect(lastSelect) {
				try {
					await uni.setStorage({
						key: "lastSelect",
						data: lastSelect
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
				
				// console.log(this.plans)
				
				getApp().globalData.isPlanChange = true
				getApp().globalData.isEditChange = false
				
				await this.savePlans()
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
	.planZone {
		padding-bottom: 128px;
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
		width: 48px;
		height: 48px;
	}
	.iconlite {
		width: 32px;
		height: 32px;
	}
</style>
