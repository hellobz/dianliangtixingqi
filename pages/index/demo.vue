<template>
	<view class="content">
		<button class="btn" @click="handleToSub" type="primary">启动电量提醒</button>
		<button class="houtai" @click="handleKeepAlive" type="primary">后台运行</button>
		<button type="primary" @click="HandleFindDieAlive">结束后台运行</button>
	</view>
</template>

<script>
	var g_wakelock = null;
	var innerAudioContext = uni.createInnerAudioContext();
	export default {
		data() {
			return {
				title: 'Hello',
				oncountNum: 1000,
				decountNum: 1000,
				countNum: 1000
			}
		},
		onLoad() {

		},
		onShow() {
			this.oncountNum = 1000;
			this.decountNum = 1000;
			this.countNum = 1000;
			this.handleToSub();
			this.handleKeepAlive();
		},
		onHide() {
			// this.handleToSub();
			// this.handleKeepAlive();
			var timeNum = 1000;
			// var countNum = 1000;
			var that = this;
			var inn = uni.createInnerAudioContext();
			let timer = setInterval(() => {
				console.log(that.countNum,'countNum')
				that.countNum += 1000;
				if (that.countNum >= 6000) {
					console.log('dingshiqi***')
					clearInterval(timer)
					innerAudioContext.stop()
				}
			}, timeNum)
			// setInterval(()=> {
			// 	console.log('hello')
			// 	this.initVal && this.initVal.stop()
			// },10000)
			// setTimeout(()=> {
			// 	console.log('hello')
			// 	this.initVal && this.initVal.stop()
			// },10000)

		},
		methods: {
			// 后台运行
			handleKeepAlive() {

				uni.showToast({
					title: '后台运行中****'
				})

				//Android  
				var main = plus.android.runtimeMainActivity();
				var Context = plus.android.importClass("android.content.Context");
				var PowerManager = plus.android.importClass("android.os.PowerManager");
				var pm = main.getSystemService(Context.POWER_SERVICE);
				g_wakelock = pm.newWakeLock(PowerManager.PARTIAL_WAKE_LOCK, "ANY_NAME");
				g_wakelock.acquire();

			},
			// 结束后台运行
			HandleFindDieAlive() {

				uni.showToast({
					title: '结束了后台运行****'
				})

				if (g_wakelock != null && g_wakelock.isHeld()) {
					g_wakelock.release();
					g_wakelock = null;
				}

			},

			// 
			handleToSub() {
				var self = this;
				var ontimerNum = 1000;
				// var oncountNum = 1000;
				// ----------------
				var detimerNum = 1000;
				// var decountNum = 1000;
			
				var main = plus.android.runtimeMainActivity();
				var Intent = plus.android.importClass('android.content.Intent');
				var recevier = plus.android.implements('io.dcloud.feature.internal.reflect.BroadcastReceiver', {
					onReceive: function(context, intent) {
						var action = intent.getAction();
						if (action == Intent.ACTION_BATTERY_CHANGED) {
							var level = intent.getIntExtra("level", 0); //电量  
							var voltage = intent.getIntExtra("voltage", 0); //电池电压  
				   var temperature = intent.getIntExtra("temperature", 0); //电池温度  
							//如需获取别的，在这里继续写，此代码只提供获取电量  
							console.log(level)

							var flag = true;
							var count = 0;
							if (flag) {
								
								if (level <= 20 && flag) {
									flag = false;
									let ontimer = setInterval(() => {
										self.oncountNum = self.oncountNum + 1000;
										console.log(self.oncountNum,'ontimer')
										if (self.oncountNum >= 6000) {
											console.log('ddd***')
											clearInterval(ontimer)
											innerAudioContext.stop()
										}
									}, ontimerNum)
									
									innerAudioContext.play()
									
									innerAudioContext.autoplay = true;
									innerAudioContext.src = 'http://axuan.pub/audio/diandi.mp3'									
									
									// count++
									// console.log('beforecount', count)
									// if (count >= 30) {
									// 	console.log(count)
									// 	innerAudioContext.pause();
									// 	flag = false;
									// }
									innerAudioContext.onPlay(() => {
										if (self.oncountNum >= 6000) {
											console.log('accc***')
											clearInterval(ontimer)
											innerAudioContext.stop()
										}
										console.log('开始播放');
									});
									innerAudioContext.onEnded(() => {
										// flag = true;
										// count++
										// if (count >= 30) {
										// 	flag = false;
										// }
									})
									innerAudioContext.onError((res) => {
										console.log(res.errMsg);
										console.log(res.errCode);
									});
									// setTimeout(()=> {
									// 	innerAudioContext.stop()
									// },10000)
								} else if (level === 100 && flag) {
									flag = false;
									
									let detimer = setInterval(() => {
										self.decountNum += 1000;
										console.log(self.decountNum,'ontimer')
										if (self.decountNum >= 6000) {
											console.log('ddd***')
											clearInterval(detimer)
											innerAudioContext.stop()
										}
									}, detimerNum)
									
									innerAudioContext.play()
									innerAudioContext.autoplay = true;
									innerAudioContext.src = 'http://axuan.pub/audio/dianman.mp3'																	
									
									innerAudioContext.onEnded(() => {
									})
									innerAudioContext.onPlay(() => {
										console.log('开始播放');
									});
									innerAudioContext.onError((res) => {
										console.log(res.errMsg);
										console.log(res.errCode);
									});
									// setTimeout(()=> {
									// 	innerAudioContext.stop()
									// },10000)

								}
							}


						}
					}
				});
				var IntentFilter = plus.android.importClass('android.content.IntentFilter');
				var filter = new IntentFilter(Intent.ACTION_BATTERY_CHANGED);
				main.registerReceiver(recevier, filter);
				uni.showToast({
					title: '成功中****'
				})
			}
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.btn {
		margin-top: 40rpx;
	}

	.houtai {
		margin: 40rpx 0;
	}
</style>
