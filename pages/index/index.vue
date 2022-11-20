<template>
	<view class="container">
		<uni-data-select label="方案" v-model="chosen" :localdata="selection"></uni-data-select>
		<view>
			<text>方案: {{chosen}}</text>
		</view>
		
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
				chosen: 1
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
