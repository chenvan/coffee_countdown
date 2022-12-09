<template>
	<view class="container">
		<uni-forms :modelValue="fromData" label-position="top">
			<uni-forms-item label="方案名" name="name">
				<uni-easyinput type="text" v-model="fromData.name"></uni-easyinput>
			</uni-forms-item>
			<uni-forms-item label="开始倒数">
				<uni-data-checkbox v-model="fromData.initCd" :localdata="range"></uni-data-checkbox>
			</uni-forms-item>
			<uni-forms-item label="关键时间点倒数">
				<uni-data-checkbox v-model="fromData.keyPtCd" :localdata="range"></uni-data-checkbox>
			</uni-forms-item>
			<uni-forms-item label="关键时间点">
				<uni-easyinput type="textarea" v-model="fromData.keyPts" placeholder="关键时间点, 用空格隔开" />
			</uni-forms-item>
		</uni-forms>
		<button @click="onClick">确认</button>
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
				fromData: {
					name: "",
					initCd: 3,
					keyPtCd: 3,
					keyPts: "",
				},
				id: null,
				errMsg: "",
				range:[{"value": 3,"text": "是"}, {"value": 0,"text": "否"}]
			}
		},
		methods: {
			onClick() {
				let name = this.fromData.name.trim()
				let keyPtsList = this.fromData.keyPts.trim().split(/\s+/)
				
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
				
				// 还需要检查关键时间点的间隔要大于3?
				
				this.$refs.confirm.open()
			},
			onConfirm() {
				//
				let temp = this.fromData.keyPts.trim().split(/\s+/).map(value => Number(value))
				temp.sort((a, b) => a - b)
				
				// console.log(temp)
				
				let plan = {
					name: this.fromData.name,
					initCd: this.fromData.initCd,
					keyPtCd: this.fromData.keyPtCd,
					keyPts: temp
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
				this.fromData.name = selectPlan.name
				this.fromData.inidCd = selectPlan.inidCd
				this.fromData.keyPtCd = selectPlan.keyPtCd
				this.fromData.keyPts = selectPlan.keyPts.join(" ")
			} else {
				this.id = getApp().globalData.plans.length
			}
		}
	}
</script>

<style>
	.container {
		padding: 8px;
		height: 100%;
		display: flex;
		flex-direction: column;
	}
</style>
