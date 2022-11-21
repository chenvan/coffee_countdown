<template>
	<view>
		<text>{{timerFormat}}</text>
	</view>
	<view>
		<button @click="start">Start</button>
		<button @click="stop">Stop</button>
		<button @click="clear">Clear</button>
	</view>
</template>

<script>
	let beepAudioCtx = null
	let	boopAudioCtx = null
	
	export default {
		props: {
			timeline: {
				require: true,
				type: Array
			}
		},
		data() {
			return {
				timer: undefined,
				timeId: null,
				beeps: [],
				boops: [],
				status: "ready",
				
			}
		},
		computed: {
			timerFormat() {
				if(this.status === "ready" || this.timer === -(this.timeline[0] + 1)) {
					return ""
				} else {
					return Math.abs(this.timer)
				}
			}
		},
		watch: {
			timeline() {
				this.clear()
			},
			timer(newT) {
				if(this.beeps.includes(newT)) {
					console.log(`${newT} beep!`)
					beepAudioCtx.play()
				}
				
				if(this.boops.includes(newT)) {
					console.log(`${newT} boop!!!`)
					boopAudioCtx.play()
				}
			}
		},
		mounted() {
			beepAudioCtx = uni.createInnerAudioContext()
			beepAudioCtx.src = '/static/beep-08b.mp3'
			boopAudioCtx = uni.createInnerAudioContext()
			boopAudioCtx.src = '/static/beep-09.mp3'
		},
		beforeDestroy() {
			beepAudioCtx = null
		},
		methods: {
			add() {
				this.timer++
			},
			start() {
				this.initTl()
				this.timeId = setInterval(this.add, 1000)
				this.status = "timing"
			},
			stop() {
				if(this.timeId !== null) {
					clearInterval(this.timeId)
					this.timeId = null
				}
			},
			clear() {
				this.stop()
				this.status = "ready"
			},
			initTl() {
				let [initCountdown, timeSet] = this.timeline
				this.timer = -(initCountdown + 1)
				
				this.beeps = [].concat(this.range(-initCountdown, 0))
				this.boops = [0]
				
				for(let [endPt, countdown] of timeSet) {
					this.beeps = this.beeps.concat(this.range(endPt - countdown, endPt))
					this.boops = this.boops.concat([endPt])
				}
			},
			range(start, stop, step = 1) {
				return Array(Math.ceil((stop - start) / step)).fill(start).map((x, y) => x + y * step)
			}
		}
	}
</script>

<style>
</style>