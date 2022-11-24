<template>
	<view class="container">
		<view class="mode">
			<uni-data-select
				label="方案" 
				v-model="chosen" 
				:localdata="selection"
			></uni-data-select>
		</view>
		<view class="timer">
			<text>{{timerFormat}}</text>
		</view>
		<view class="ctrlZone">
			<view class="btn" @click="start" v-if="status !== 'timing'">
				<image class="icon" src="/static/play_go.png" mode="aspectFit"></image>
			</view>
			<view class="btn" @click="stop" v-if="status === 'timing'">
				<image class="icon" src="/static/play_pause.png" mode="aspectFit"></image>
			</view>
			<view class="btn" @click="clear" v-if="status !== 'ready'">
				<image class="icon" src="/static/close-circle-fill.png" mode="aspectFit"></image>
			</view>
		</view>
	</view>
</template>

<script>
	let beepAudioCtx = null
	let	boopAudioCtx = null
	
	function zeroPad(num, places) {
	  var zero = places - num.toString().length + 1;
	  return Array(+(zero > 0 && zero)).join("0") + num;
	}
	
	function range(start, stop, step = 1) {
		return Array(Math.ceil((stop - start) / step)).fill(start).map((x, y) => x + y * step)
	}
	
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
				],
				timer: undefined,
				timeId: null,
				status: "ready",
			}
		},
		computed: {
			timerFormat() {
				if(this.status === "ready") {
					return "Ready"
				}else if(this.timer === -(this.timeline[0] + 1)) {
					return "Go"
				}else {
					let temp = Math.abs(this.timer)
					return `${zeroPad(Math.floor(temp / 60), 2)} : ${zeroPad(temp % 60, 2)}`
				}
			},
			timeline() {
				return this.timelineList[this.chosen]
			},
			beeps() {
				let [initCountdown, timeSet] = this.timeline
				
				let temp = range(-initCountdown, 0)
				
				for(let [endPt, countdown] of timeSet) {
					temp = temp.concat(range(endPt - countdown, endPt))
				}
				
				return temp
			},
			boops() {
				let timeSet = this.timeline[1]
				
				let temp = [0]
				
				for(let [endPt, _] of timeSet) {
					temp = temp.concat(endPt)
				}
				
				return temp
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
		onLoad() {
			beepAudioCtx = wx.createInnerAudioContext({
				useWebAudioImplement: true
			})
			beepAudioCtx.src = '/static/start.mp3'
			
			boopAudioCtx = wx.createInnerAudioContext({
				useWebAudioImplement: true
			})
			
			boopAudioCtx.src = '/static/beep-09.mp3'
			
			this.clear()
		},
		onUnload() {
			beepAudioCtx = null
			boopAudioCtx = null
		},
		methods: {
			add() {
				this.timer++
			},
			start() {
				if(this.timeId === null) {
					this.timeId = setInterval(this.add, 1000)
					this.status = "timing"
				}
			},
			stop() {
				if(this.timeId !== null) {
					clearInterval(this.timeId)
					this.timeId = null
					this.status = "hang"
				}
			},
			clear() {
				this.stop()
				this.status = "ready"
				let initCountdown = this.timeline[0]
				this.timer = -(initCountdown + 1)
			}
			// },
			// range(start, stop, step = 1) {
			// 	return Array(Math.ceil((stop - start) / step)).fill(start).map((x, y) => x + y * step)
			// }
		}
	}
</script>

<style>
	.container {
		padding: 20px;
		font-size: 24px;
		line-height: 24px;
		height: 100%;
		display: flex;
		flex-direction: column;
		justify-content: space-between;	
	}
	.mode {
		flex-grow: 2;
	}
	.timer {
		align-self: center;
		font-size: 64px;
		font-weight: bold;
		font-style: italic;
		flex-grow: 2;
		/* height: 50% */
	}
	.ctrlZone {
		display: flex;
		justify-content: space-around;
		flex-grow: 1;
		height: 20%
	}
	.btn {
		width: 20%;
	}
	.icon {
		width: 100%;
	}
</style>
