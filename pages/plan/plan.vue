<template>
	<view v-if="status === 'loading'" class="loading">
		<text>Loading</text>
	</view>
	<view v-else class="page">
		<uni-swipe-action>
		    <uni-swipe-action-item 
				v-for="(name, index) in nameList"
				:key="index + name"
				:right-options="options"  
				@click="onClick" 
				@change="swipeChange($event, index)"
			>
				<view>{{name}}</view>
		    </uni-swipe-action-item>
		</uni-swipe-action>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				status: "loading",
				nameList: [],
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
			onClick(e){
				console.log(e)
				console.log('点击了'+(e.position === 'left' ? '左侧' : '右侧') + e.content.text + '按钮')
			},
			swipeChange(e,index){
				console.log('当前状态：'+ e +'，下标：' + index)
			},
			toEdit() {
				uni.navigateTo({
					url: "/pages/edit/edit"
				})
			}
		}, 
		async onLoad() {
			try {
				let { data } =  await uni.getStorage({
					key: "modeDict",
				})
				
				this.nameList = data.nameList
				this.status = "done"
				
			} catch(err) {
				console.log(err)
			}
		}
	}
</script>

<style>

</style>
