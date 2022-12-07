<template>
	<view class="container">
		<view v-if="status === 'loading'" class="loading">
			<text>Loading</text>
		</view>
		<view v-else class="main">
			<view class="plan">
				<uni-data-select
					label="方案" 
					v-model="lastSelect" 
					:localdata="selection"
				></uni-data-select>
				<view class="editBtn" @click="toEdit">
					编辑方案
				</view>
			</view>
			<view class="detail">
				<text>{{detail}}</text>
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
				lastSelect: null,
				plans: [],
				timer: null,
				timeId: null,
				status: "loading",
			}
		},
		computed: {
			selection() {
				return this.plans.map((mode, index) => {
					return {
						"value": index,
						"text": mode.name
					}
				})
			},
			timerFormat() {
				if(this.status === "loading") {
					return ""
				}else if(this.status === "ready") {
					return "Ready"
				}else if(this.timer === -(this.chosenMode.initCd + 1)) {
					return "Go"
				}else {
					let temp = Math.abs(this.timer)
					return `${zeroPad(Math.floor(temp / 60), 2)} : ${zeroPad(temp % 60, 2)}`
				}
			},
			detail() {
				return this.chosenMode === null ? "" : `[${this.chosenMode.keyPts.join(", ")}]`
			},
			chosenMode() {
				if(this.lastSelect !== null && this.plans.length > 0) {
					return this.plans[this.lastSelect]
				} else {
					return null
				}
			},
			beeps() {
				let temp = []
				
				if(this.chosenMode !== null) {
					let {initCd, keyPtCd, keyPts} = this.chosenMode
					
					temp = temp.concat(range(-initCd, 0))
					
					keyPts.forEach(keyPt => {
						temp = temp.concat(range(keyPt - keyPtCd, keyPt))
					})
				}
				
				return temp
			},
			boops() {
				let temp = [0]
				
				if(this.chosenMode !== null) {
					temp = temp.concat(this.chosenMode.keyPts)
				}
				
				return temp
			}
		},
		watch: {
			chosenMode() {
				if(this.chosenMode !== null) {
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
						key: "lastSelect"
					}),
					uni.getStorage({
						key: "plans",
					})
				])

				this.lastSelect = storage[0].data
				this.plans = storage[1].data
				
			} catch(err) {
				console.log(err)
				console.log("set default mode")
			
				
				this.lastSelect = 0
				this.plans = [
					{
						name: "聪明杯",
						initCd: 3,
						keyPtCd: 3,
						keyPts: [180, 210]
					},
					{
						name: "爱乐压",
						initCd: 3,
						keyPtCd: 3,
						keyPts: [120, 150]
					},
					{
						name: "金龙鱼冲泡法",
						initCd: 3,
						keyPtCd: 3,
						keyPts: [30, 60]
					},
					{
						name: "霍夫曼 V60",
						initCd: 3,
						keyPtCd: 3,
						keyPts: [45, 70, 90, 110, 180]
					},
				],
				
				await Promise.all([
					uni.setStorage({
						key: "plans",
						data: this.plans
					}),
					uni.setStorage({
						key: "lastSelect",
						data: this.lastSelect
					}),
				])
			}
			
		},
		onShow () {
			if(getApp().globalData.isChange) {
				getApp().globalData.isChange = false
				this.lastSelect = getApp().globalData.lastSelect
				this.plans = getApp().globalData.plans
			}
		},
		async onHide() {

			await uni.setStorage({
				key: "lastSelect",
				data: this.lastSelect
			})
		},
		methods: {
			add() {
				this.timer++
			},
			start() {
				if(this.timeId === null && this.lastSelect !== "") {
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
				this.timer = -(this.chosenMode.initCd + 1)
			},
			toEdit() {
				getApp().globalData.lastSelect = this.lastSelect
				getApp().globalData.plans = this.plans
				
				uni.navigateTo({
					url: "/pages/plan/plan"
				})
				
				
			}
		}
	}
</script>

<style>
	.container {
		padding: 20px;
		height: 100%;
	}
	.loading {
		display: flex;
		height: 100%;
		flex-direction: column;
		justify-content: center;
		align-items: center;
	}
	.main {
		display: flex;
		flex-direction: column;
		justify-content: space-between;	
		height: 100%;
	}
	.plan {
		display: flex;
		flex-direction: column;
	}
	.editBtn {
		align-self: flex-end;
		margin-top: 12px;
		font-size: 16px;
		font-weight: 300;
		color: cadetblue;
	}
	.detail {
		margin-top: 24px;
		font-size: 16px;
		color: grey;
		font-style: normal;
		align-self: center;
	}
	.timer {
		align-self: center;
		font-size: 64px;
		font-weight: bold;
		font-style: italic;
		flex-grow: 2;
		display: flex;
		align-items: center;
	}
	.ctrlZone {
		display: flex;
		justify-content: space-around;
		max-height: 15%;
	}
	.btn {
		width: 64px;
	}
	.icon {
		width: 100%;
		max-height: 100%;
	}
	/* uni-data-select */
	.uni-select__selector-item text {
		font-size: 18px;
	}
	.uni-select__input-text {
		font-size: 18px;
	}
</style>
