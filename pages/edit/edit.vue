<template>
	<view>
		<uni-forms :modelValue="fromData">
			<uni-forms-item label="方案名" name="name">
				<uni-easyinput type="text" v-model="fromData.name"></uni-easyinput>
			</uni-forms-item>
			<uni-forms-item label="开始前倒数">
				<uni-data-checkbox v-model="fromData.isInitCd" :localdata="range"></uni-data-checkbox>
			</uni-forms-item>
			<uni-forms-item label="关键点倒数">
				<uni-data-checkbox v-model="fromData.isTimePointCd" :localdata="range"></uni-data-checkbox>
			</uni-forms-item>
			<uni-forms-item label="关键点">
				<uni-easyinput type="textarea" v-model="fromData.timePoints" placeholder="关键时间点, 用空格隔开" />
			</uni-forms-item>
		</uni-forms>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				fromData: {
					name: "",
					isInitCd: 0,
					isTimePointCd: 0,
					timePoints: ""
				},
				
				range:[{"value": 0,"text": "是"}, {"value": 1,"text": "否"}]
			}
		},
		methods: {
			
		}
	}
</script>

<style>

</style>
