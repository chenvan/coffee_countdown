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
			<uni-popup-dialog title="关键时间点有问题" content="!"></uni-popup-dialog>
		</uni-popup>
		<uni-popup ref="confirm" type="center">
			<uni-popup-dialog title="确认删除" @confirm="onConfirm"></uni-popup-dialog>
		</uni-popup>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				fromData: {
					id: null,
					name: "",
					initCd: 3,
					keyPtCd: 3,
					keyPts: ""
				},
				range:[{"value": 3,"text": "是"}, {"value": 0,"text": "否"}]
			}
		},
		methods: {
			onClick() {
				// 检查格式, keyPts 都是数字, 且从小到大的排列
				
				// 确认
			},
			onConfirm() {
				
			}
		},
		onLoad(options) {
			if(options.id) {
				this.fromData.id = options.id
				let selectPlan = getApp().globalData.plans[options.id]
				console.log(options.id)
				console.log(selectPlan)
				this.fromData.name = selectPlan.name
				this.fromData.inidCd = selectPlan.inidCd
				this.fromData.keyPtCd = selectPlan.keyPtCd
				this.fromData.keyPts = selectPlan.keyPts.join(" ")
			} else {
				this.fromData.id = getApp().globalData.plans.length
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
