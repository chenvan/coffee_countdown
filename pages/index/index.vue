<template>
	<view class="container">
		<view v-if="status === 'loading'" class="loading">
			<text>Loading</text>
		</view>
		<view v-else class="app">
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
				chosen: null,
				modeDict: {
					"nameList": [],
					"timelineList": []
				},
				timer: null,
				timeId: null,
				status: "loading",
			}
		},
		computed: {
			selection() {
				return this.modeDict.nameList.map((name, index) => {
					return {
						"value": index,
						"text": name
					}
				})
			},
			timelineList() {
				return this.modeDict.timelineList
			},
			timerFormat() {
				if(this.status === "loading") {
					return ""
				}else if(this.status === "ready") {
					return "Ready"
				}else if(this.timer === -(this.timeline[0] + 1)) {
					return "Go"
				}else {
					let temp = Math.abs(this.timer)
					return `${zeroPad(Math.floor(temp / 60), 2)} : ${zeroPad(temp % 60, 2)}`
				}
			},
			timeline() {
				if(this.chosen !== null && this.timelineList.length > 0) {
					return this.timelineList[this.chosen]
				} else {
					return null
				}
			},
			beeps() {
				let temp = []
				
				if(this.timeline !== null) {
					let [initCountdown, timeSet] = this.timeline
					
					temp = temp.concat(range(-initCountdown, 0))
					
					for(let [endPt, countdown] of timeSet) {
						temp = temp.concat(range(endPt - countdown, endPt))
					}
				}
				
				return temp
			},
			boops() {
				let temp = [0]
				
				if(this.timeline !== null) {
					let timeSet = this.timeline[1]
					
					for(let [endPt, _] of timeSet) {
						temp = temp.concat(endPt)
					}
				}
				
				return temp
			}
		},
		watch: {
			timeline() {
				// timeline 变成另一个有效的 timeline
				if(this.timeline !== null) {
					this.clear()
				}
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
		async onLoad() {
			beepAudioCtx = wx.createInnerAudioContext({
				useWebAudioImplement: true
			})
			beepAudioCtx.src = '/static/start.mp3'
			
			boopAudioCtx = wx.createInnerAudioContext({
				useWebAudioImplement: true
			})
			
			boopAudioCtx.src = '/static/beep-09.mp3'
			
			try {
				let storage =  await Promise.all([
					uni.getStorage({
						key: "lastChosen"
					}),
					uni.getStorage({
						key: "modeDict",
					})
				])
				
				this.chosen = storage[0].data
				this.modeDict = storage[1].data
				
			} catch(err) {
				console.log(err)
				console.log("set default mode")
				
				this.chosen = 0
				this.modeDict = {
					nameList: ["聪明杯", "爱乐压", "金龙鱼冲泡法"],
					timelineList: [
						[3, [[180, 3], [210, 3]]],
						[3, [[120, 3], [150, 3]]],
						[3, [[30, 3], [60, 3]]],
					]
				}
				
				await Promise.all([
					uni.setStorage({
						key: "modeDict",
						data: this.modeDict
					}),
					uni.setStorage({
						key: "lastChosen",
						data: this.chosen
					}),
				])
			}
			
		},
		async onUnload() {
			// 这里有问题
			console.log("unload")
			beepAudioCtx = null
			boopAudioCtx = null
			
			await uni.setStorage({
				key: "lastChosen",
				data: this.chosen
			})
		},
		methods: {
			add() {
				this.timer++
			},
			start() {
				if(this.timeId === null && this.chosen !== "") {
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
		}
	}
</script>

<style>
	.container {
		padding: 20px;
		font-size: 24px;
		line-height: 24px;
		height: 100%;
	}
	.loading {
		display: flex;
		height: 100%;
		flex-direction: column;
		justify-content: center;
		align-items: center;
	}
	.app {
		display: flex;
		flex-direction: column;
		justify-content: space-between;	
		height: 100%;
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
