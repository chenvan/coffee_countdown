<template>
	<view>
		<text>{{timerShow}}</text>
	</view>
	<view>
		<button @click="start">Start</button>
		<button @click="stop">Stop</button>
		<button @click="clear">Clear</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				timer: 0,
				timeId: null
			}
		},
		computed: {
			timerShow() {
				return this.timer
			}
		},
		methods: {
			add() {
				this.timer++
			},
			start() {
				this.timeId = setInterval(this.add, 1000)
			},
			stop() {
				if(this.timeId !== null) {
					clearInterval(this.timeId)
					this.timeId = null
				}
			},
			clear() {
				this.stop()
				this.timer = 0
			}
		}
	}
</script>

<style>
</style>