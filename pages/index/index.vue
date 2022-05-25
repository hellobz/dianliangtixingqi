<template>
	<view class="content">
		<button class="btn" @click="handleToSub" type="primary">启动电量提醒</button>
		<button class="houtai" @click="handleKeepAlive" type="primary">后台运行</button>
		<button type="primary" @click="handleFindDieAlive">结束后台运行</button>
		<button class="houtai" type="primary" @click="handleClickStart">开始</button>
		<button type="primary" @click="handleEnd">停止</button>
	</view>
</template>

<script>
	var g_wakelock = null;
	var innerAudioContext = uni.createInnerAudioContext();
	export default {
		data() {
			return {
			}
		},
		onLoad() {

		},
		onShow() {
			// 启动
			this.handleToSub();
			// 后台运行
			this.handleKeepAlive();
		},
		onHide() {
			// 启动
			this.handleToSub()
			// 后台运行
			this.handleKeepAlive()

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
			handleFindDieAlive() {

				uni.showToast({
					title: '结束了后台运行****'
				})

				if (g_wakelock != null && g_wakelock.isHeld()) {
					g_wakelock.release();
					g_wakelock = null;
				}

			},
			
			// 点击开始 
			handleClickStart() {
				this.handleToSub()
			},
			
			// 直接开始 
			handleStart() {
				innerAudioContext.play()
			},
			
			// 结束
			handleEnd() {
				innerAudioContext.stop()
			},

			// 启动
			handleToSub() {
				var self = this;
			
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

							if (level <= 20) {
							
								
								
								innerAudioContext.autoplay = true;
								innerAudioContext.src = 'http://axuan.pub/audio/diandi.mp3'	
																														
								this.handleStart()																						
														
								innerAudioContext.onEnded(() => {
								
								})
								innerAudioContext.onError((res) => {
									console.log(res.errMsg);
									console.log(res.errCode);
								});
							
							} else if (level === 100) {
				
								innerAudioContext.autoplay = true;
								innerAudioContext.src = 'http://axuan.pub/audio/dianman.mp3'																	
								
								this.handleStart()
								
								innerAudioContext.onEnded(() => {
								})
						
								innerAudioContext.onError((res) => {
									console.log(res.errMsg);
									console.log(res.errCode);
								});								
							
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
