<template>
	<view @click="onClick(id)" class="container">
		<view class="content">
			<view class="title">{{item.name}}</view>
			<view class="detail">
				<view>{{item.keyPts.join(",")}}</view>
				<view>{{item.memo}}</view>
			</view>
		</view>
		<view class="select">
			<radio v-if="isDelMode" :checked="isSelected"></radio>
		</view>
	</view>
</template>

<script>
	export default {
		name:"PlanItem",
		props: ["item", "id", "isDelMode", "isSelected"],
		emits: ['select'],
		data() {
			return {
				
			};
		},
		methods: {
			onClick(id) {
				if(!this.isDelMode) {
					uni.navigateTo({					
						url: `/pages/edit/edit?id=${id}`
					})
				} else {
					this.$emit("select", this.id)
				}
			},
		}
	}
</script>

<style>
	.container {
		height: 100px;
		margin: 8px;
		border-radius: 16px;
		background-color: white;
		display: flex;
		direction: row;
		justify-content: space-between;
	}
	.content {
		margin: 8px;
	}
	.select {
		align-self: center;
		margin-right: 8px;
	}
	.title {
		font-size: 18px;
		margin: 4px 0;
	}
	.detail {
		font-size: 14px;
	}
</style>