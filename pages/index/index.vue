<template>
	<view class="container">
		<uni-data-select label="方案" v-model="chosen" :localdata="selection"></uni-data-select>
		<countdown :timeline="timelineList[chosen]"></countdown>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				selection: [
					{value: 0, text: "聪明杯"}, 
					{value: 1, text: "爱乐压"}, 
					{value: 2, text: "金龙鱼冲泡法"},
				],
				chosen: 1,
				timelineList: [
					[3, [[180, 3], [210, 3]]],
					[3, [[120, 3], [150, 3]]],
					[3, [[30, 3], [60, 3]]],
				]
			}
		},
		methods: {

		}
	}
</script>

<style>
	.container {
		padding: 20px;
		font-size: 14px;
		line-height: 24px;
	}
</style>
