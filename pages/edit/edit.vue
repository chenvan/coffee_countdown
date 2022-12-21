<template>
	<view class="main">
		<view class="part">
			<view class="label">方案名</view>
			<input class="input" v-model="name" />
		</view>
		<view class="part">
			<view class="label">倒计时</view>
			<checkbox-group @change="onChange($event)">
				<checkbox class="checkUnit" :value="'init'" :checked="initCd">开始</checkbox>
				<checkbox class="checkUnit" :value="'keyPt'" :checked="keyPtCd">关键时间点</checkbox>
			</checkbox-group>
		</view>
		<view class="part">
			<view class="label">关键时间点</view>
			<!-- input type 不能使用 number 或者 digit -->
			<input class="input" v-model="keyPts" />
		</view>
		<view class="part">
			<view class="label">备注</view>
			<textarea class="input" v-model="memo" />
		</view>
		<view class="ctrlZone">
			<button @click="onClick">确认</button>
		</view>	
		<uni-popup ref="errMsg" type="center">
			<uni-popup-dialog title="问题" :content="errMsg"></uni-popup-dialog>
		</uni-popup>
		<uni-popup ref="confirm" type="center">
			<uni-popup-dialog title="确认添加" @confirm="onConfirm"></uni-popup-dialog>
		</uni-popup>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				name: "",
				initCd: 3,
				keyPtCd: 3,
				keyPts: "",
				memo: "",
				id: null,
				errMsg: ""
			}
		},
		methods: {
			onClick() {
				let name = this.name.trim()
				let keyPtsList = this.keyPts.trim().split(/\s+/)
				
				if(name.length === 0) {
					this.errMsg = "方案没有名字"
					this.$refs.errMsg.open()
					return
				}
				
				if(!keyPtsList.every(value => Number.isInteger(Number(value)) && Number(value) > 0)) {
					this.errMsg = "关键时间点不符合填写要求"
					this.$refs.errMsg.open()
					return
				}
				
				this.$refs.confirm.open()
			},
			onChange(e) {
				this.initCd = e.detail.value.includes("init") ? 3 : 0
				this.keyPtCd = e.detail.value.includes("keyPt") ? 3 : 0
			},
			onConfirm() {
				//
				let temp = this.keyPts.trim().split(/\s+/).map(value => Number(value))
				temp.sort((a, b) => a - b)
				
				let plan = {
					name: this.name.trim(),
					initCd: this.initCd,
					keyPtCd: this.keyPtCd,
					keyPts: temp,
					memo: this.memo !== undefined ? this.memo.trim() : ""
				}
				
				if(this.id === getApp().globalData.plans.length) {
					getApp().globalData.plans = getApp().globalData.plans.concat(plan)
				} else {
					getApp().globalData.plans[this.id] = plan
				}
				
				getApp().globalData.isEditChange = true
				
				uni.navigateBack()
			}
		},
		onLoad(options) {
			if(options.id) {
				this.id = options.id
				let selectPlan = getApp().globalData.plans[options.id]
				this.name = selectPlan.name
				this.initCd = selectPlan.initCd
				this.keyPtCd = selectPlan.keyPtCd
				this.keyPts = selectPlan.keyPts.join(" ")
				this.memo = selectPlan.memo
			} else {
				this.id = getApp().globalData.plans.length
			}
		}
	}
</script>

<style>
	.main {
		height: 100%;
		display: flex;
		flex-direction: column;
	}
	.part {
		margin: 8px;
	}
	.label {
		margin-bottom: 4px;
		font-size: 20px;
	}
	.checkUnit {
		margin-right: 8px;
	}
	.input {
		background-color: white;
		width: 100%;
		min-height: 32px;
	}
	.ctrlZone {
		position: fixed;
		bottom: 32px;
		right:0;
		width: 100%;
		display: flex;
		justify-content: flex-end;
	}
</style>
