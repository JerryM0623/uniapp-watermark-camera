<template>
	<view class="main-container">
		<view class="top-tools-bar">
			<view class="left">
				<view class="icon">
					<image class="setting" src="../../static/watermark-camera/top-tool-var/icon-setting.svg"></image>
				</view>
			</view>
			<view class="right">
				<view class="icon" @click="handleTimer">
					<image class="timer" src="../../static/watermark-camera/top-tool-var/icon-timer.svg" mode=""></image>
				</view>
				<view class="icon" @click="handleLighting">
					<image class="lighting" src="../../static/watermark-camera/top-tool-var/icon-lighting.svg"></image>
				</view>
				<view class="icon" @click="handleReverse">
					<image class="reverse" :class="{reversing: isReversing}" 
						src="../../static/watermark-camera/top-tool-var/icon-reverse.svg"></image>
				</view>
			</view>
		</view>
		<view class="middle-camera-review">
			<camera v-if="showCamera" class="watermark-camera" :device-position="isBack ? 'back' : 'front'" 
				:flash="lightingSettingValue" @initdone="initdone" @error="error"></camera>
		</view>
		<view class="bottom-tools-bar">
			<view class="tools-content">
				<view class="tool photo-album">
					<view class="icon thumbnail"></view>
					<view class="text">相册</view>
				</view>
				<view class="tool photo-location" @click="addLocation">
					<image class="icon location" src="../../static/watermark-camera/bottom-tool-bar/icon-location.svg"></image>
					<view class="text">地点</view>
				</view>
				<view class="tool take-photo-btn" @click="takePhoto">
					<view class="border" :class="{touch: isClick}" @click=takePhoto></view>
					<!-- <view class="button"></view> -->
				</view>
				<view class="tool photo-filter">
					<image class="icon filter" src="../../static/watermark-camera/bottom-tool-bar/icon-filter.svg"></image>
					<view class="text">滤镜</view>
				</view>
				<view class="tool photo-watermark">
					<image class="icon watermark" src="../../static/watermark-camera/bottom-tool-bar/icon-watermark.svg"></image>
					<view class="text">水印</view>
				</view>
			</view>
		</view>
		<view class="lighting-select-modal" :class="{show: isShowLighting}">
			<view class="items-bar" @click="changeLightingSetting">
				<view data-index="0" data-lightmode="auto" class="select-item" :class="{'select-it': lightingSettingIndex === '0'}">自动</view>
				<view data-index="1" data-lightmode="torch" class="select-item" :class="{'select-it': lightingSettingIndex === '1'}">常亮</view>
				<view data-index="2" data-lightmode="on" class="select-item" :class="{'select-it': lightingSettingIndex === '2'}">开启</view>
				<view data-index="3" data-lightmode="off" class="select-item" :class="{'select-it': lightingSettingIndex === '3'}">关闭</view>
			</view>
		</view>
		<view class="timer-select-modal" :class="{show: isShowTimer}">
			<view class="items-bar" @click="changeTimerSetting">
				<view data-index="0" data-timermode="off" class="select-item" :class="{'select-it': timerSettingIndex === '0'}">关闭</view>
				<view data-index="1" data-timermode="3" class="select-item" :class="{'select-it': timerSettingIndex === '1'}">3S</view>
				<view data-index="2" data-timermode="5" class="select-item" :class="{'select-it': timerSettingIndex === '2'}">5S</view>
				<view data-index="3" data-timermode="10" class="select-item" :class="{'select-it': timerSettingIndex === '3'}">10S</view>
			</view>
		</view>
		<view v-if="showCamera" class="photo-watermark-area">
			<view class="content">
				<view class="item time">时间：{{ watermarkData.time === '' ? '加载中...' : watermarkData.time }}</view>
				<view class="item location">地点：{{ watermarkData.location === '' ? '请点击地点按钮手动输入...' : watermarkData.location }}</view>
				<view class="item longitude">精度：{{ watermarkData.longitude === '' ? '正在获取中...' : watermarkData.longitude }}</view>
				<view class="item latitude">纬度：{{ watermarkData.latitude === '' ? '正在获取中...' : watermarkData.latitude }}</view>
				<!-- <view class="item weather">天气：晴 31℃</view> -->
			</view>
		</view>
		<view v-show="isShowTakePhotoMask" class="take-photo-mask"></view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// 相机API实例
				cameraContext: null,
				
				// 用于界面UI变动
				showCamera: false,
				isClick: false,
				isReversing: false,
				isShowLighting: false,
				lightingSettingIndex: '0',
				isShowTimer: false,
				timerSettingIndex: '0',
				isShowTakePhotoMask: false,
				takePhotoTimer: null,
				reverseTimer: null,
				
				// 是否启用后置摄像头
				isBack: true,
				// 存储设置的闪光灯设置
				lightingSettingValue: "auto",
				// 倒计时拍摄上设置
				timerSettingValue: 'off',
				
				// 水印数据
				watermarkData: {
					time: '',
					location: '',
					longitude: '',
					latitude: ''
				},
				updateDateTimeTimer: null
			}
		},
		methods: {
			onShow() {
				setTimeout(() => {
					this.showCamera = true
				}, 200)
				this.updateDateTime()
				this.updateDateTimeTimer = setInterval(this.updateDateTime, 30000);
				this.getLocation()
			},
			onHide() {
				if (this.updateDateTimeTimer) {
					clearInterval(this.updateDateTimeTimer)
				}
			},
			initdone() {
				const cameraContext = uni.createCameraContext()
				if (!cameraContext) {
					uni.showModal({
						title: "注意",
						content:"相机初始化失败，请退出重试！！！",
						showCancel: false,
						confirmColor: "#dd524d",
						success: () => {
							uni.navigateBack()
						}
					})
					return;
				}
				this.cameraContext = cameraContext
			},
			error() {
				console.error("相机加载失败！！！")
				// uni.showModal({
				// 	title: "注意",
				// 	content:"当前设备暂不支持相机！！！",
				// 	showCancel: false,
				// 	confirmColor: "#dd524d",
				// 	success: () => {
				// 		uni.navigateBack()
				// 	}
				// })
			},
			takePhoto() {
				// 启用UI变换
				this.isClick = true
				
				// 防止多次快速点击
				if (!this.takePhotoTimer) {
					
					// 开始拍摄照片
					if (this.cameraContext) {
						if (this.timerSettingValue !== 'off') {
							setTimeout(() => {
								this.isShowTakePhotoMask = true
								this.handleTakePhoto()
							}, Number(this.timerSettingValue) * 1000)
						} else {
							this.isShowTakePhotoMask = true
							this.handleTakePhoto()
						}
					}
					
					this.takePhotoTimer = setTimeout(() => {
						this.isClick = false
						this.takePhotoTimer = null
					},200)
				}
			},
			handleTakePhoto() {
				this.cameraContext.takePhoto({
					quality: 'normal',
					success: (res) => {
						const { tempImagePath } = res
						if (tempImagePath) {
							console.log("拍照结束，图片路径为：", tempImagePath)
							uni.navigateTo({
								url: `/pages/renderPhoto/renderPhoto?tempImagePath=${tempImagePath}&time=${this.watermarkData.time}&location=${this.watermarkData.location}&longitude=${this.watermarkData.longitude}&latitude=${this.watermarkData.latitude}`
							})
						}
					},
					complete: () => {
						this.isShowTakePhotoMask = false
					}
				})
			},
			handleReverse() {
				this.isShowLighting = false
				this.isShowTimer = false
				
				// 启用按钮旋转
				this.isReversing = true
				// 防止快速多次点击
				if (!this.reverseTimer) {
					// 切换相机前后镜头
					this.isBack = !this.isBack
					this.reverseTimer = setTimeout(() => {
						this.isReversing = false
						this.reverseTimer = null
					},2000)
				}
			},
			handleLighting() {
				this.isShowTimer = false
				this.isShowLighting = !this.isShowLighting
			},
			changeLightingSetting(e) {
				const lightingIndex = e.target.dataset.index
				const lightingValue = e.target.dataset.lightmode
				
				if (lightingIndex) {
					this.lightingSettingIndex = lightingIndex
				}
				if (lightingValue) {
					this.lightingSettingValue = lightingValue
				}
			},
			handleTimer() {
				this.isShowLighting = false
				this.isShowTimer = !this.isShowTimer
			},
			changeTimerSetting(e) {
				const timerIndex = e.target.dataset.index
				const timerValue = e.target.dataset.timermode
				
				if (timerIndex) {
					this.timerSettingIndex = timerIndex
				}
				if (timerValue) {
					this.timerSettingValue = timerValue
				}
			},
			getLocation() {
				uni.getLocation({
					type: 'wgs84',
					altitude: false,
					isHighAccuracy: true,
					success: (res) => {
						const longitude = res.longitude < 0 ? Math.abs(res.longitude).toString() + 'W' : Math.abs(res.longitude).toString() + 'E' 
						const latitude = res.latitude < 0 ? Math.abs(res.latitude).toString() + 'S' : Math.abs(res.latitude).toString() + 'N'
						this.watermarkData.longitude = longitude
						this.watermarkData.latitude = latitude
					},
					fail: (error) => {
						console.error("定位获取失败", error.errMsg)
					}
				})
			},
			addLocation() {
				uni.showModal({
					title: '添加地点',
					editable: true,
					placeholderText: '请输入所在地名称',
					success: (res) => {
						if (res.confirm) {
							if (!res.content) {
								uni.showToast({
									title: '请填写地点',
									icon: 'none',
									duration: 2000
								});
							} else {
								this.watermarkData.location = res.content
							}
						}
					}
				});
			},
			getCurrentDateTime() {
				const now = new Date();
				const year = now.getFullYear();
				const month = String(now.getMonth() + 1).padStart(2, '0');
				const day = String(now.getDate()).padStart(2, '0');
				const hours = String(now.getHours()).padStart(2, '0');
				const minutes = String(now.getMinutes()).padStart(2, '0');
				
				return `${year}.${month}.${day} ${hours}:${minutes}`;
			},
			updateDateTime() {
			  const currentDateTime = this.getCurrentDateTime();
			  this.watermarkData.time = currentDateTime
			}
		}
	}
</script>

<style scoped lang="less">
.main-container {
	width: 750rpx;
	height: 100vh;
	.top-tools-bar {
		width: 750rpx;
		height: 100rpx;
		background-color: #000000;
		
		display: flex;
		flex-direction: row;
		align-items: center;
		justify-content: space-between;
		
		position: relative;
		z-index: 10;
		
		.left {
			width: 100rpx;
			height: 100rpx;
			.icon {
				width: 100rpx;
				height: 100rpx;
				position: relative;
				image {
					position: absolute;
					top: 25rpx;
					left: 25rpx;
					width: 50rpx;
					height: 50rpx;
				}
			}
		}
		.right {
			width: 300rpx;
			height: 100rpx;
			
			display: flex;
			flex-direction: row;
			align-items: center;
			justify-items: flex-start;
			.icon {
				width: 100rpx;
				height: 100rpx;
				position: relative;
				image {
					position: absolute;
					top: 25rpx;
					left: 25rpx;
					width: 50rpx;
					height: 50rpx;
					
					&.reversing {
						animation: reversing 2s;
					}
				}
			}
		}
	}
	.middle-camera-review{
		width: 750rpx;
		height: calc(100vh - 400rpx);
		background-color: #000;
		
		.watermark-camera {
			width: 100%;
			height: 100%;
		}
	}
	.bottom-tools-bar{
		width: 750rpx;
		height: 300rpx;
		background-color: #000000;
		
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		
		.tools-content {
			width: 750rpx;
			height: 150rpx;
			
			display: flex;
			flex-direction: row;
			flex-wrap: nowrap;
			justify-content: center;
			align-items: center;
			
			.tool {
				width: 150rpx;
				height: 150rpx;
				
				display: flex;
				flex-direction: column;
				align-items: center;
				justify-content: center;
				
				&.take-photo-btn {
					position: relative;
					.border{
						width: 126rpx;
						height: 126rpx;
						border: 12rpx solid #ffffff;
						border-radius: 50%;
						position: absolute;
						top: 0;
						left: 0;
						&.touch {
							animation: takephoto 0.2s;
						}
					}
					// .button{
					// 	width: 110rpx;
					// 	height: 110rpx;
					// 	border-radius: 50%;
					// 	background-color: #ffffff;
					// 	position: absolute;
					// 	top: 50%;
					// 	left: 50%;
					// 	transform: translate(-50%, -50%);
					// }
				}
				
				.icon {
					width: 50rpx;
					height: 50rpx;
				}
				
				.thumbnail {
					border-radius: 50%;
					background-color: #d9d9d9;
				}
				
				.text {
					text-align: center;
					color: #ffffff;
					font-size: 24rpx;
					margin-top: 10rpx;
				}
			}
		}
	}
	.lighting-select-modal {
		box-sizing: border-box;
		width: 750rpx;
		height: 100rpx;
		padding: 0 20rpx;
		background-color: rgba(0, 0, 0, 0.8);
		position: fixed;
		top: -100rpx;
		left: 0;
		
		display: flex;
		flex-direction: row;
		align-items: center;
		justify-content: center;
		
		transition: all 0.3s;
		z-index: 9;
		
		&.show {
			top: 100rpx;
			left: 0;
		}
		
		.items-bar {
			width: 100%;
			height: 50rpx;
			color: #fff;
			border-radius: 10rpx;
			overflow: hidden;
			background-color: #000;
			
			display: flex;
			flex-direction: row;
			align-items: center;
			justify-content: center;
			
			.select-item {
				flex: 1;
				height: 50rpx;
				line-height: 50rpx;
				text-align: center;
				font-size: 24rpx;
				transition: all 0.3s;
				
				&.select-it {
					background-color: darkorange;
				}
			}
		}
	}
	.timer-select-modal {
		box-sizing: border-box;
		width: 750rpx;
		height: 100rpx;
		padding: 0 20rpx;
		background-color: rgba(0, 0, 0, 0.8);
		position: fixed;
		top: -100rpx;
		left: 0;
		
		display: flex;
		flex-direction: row;
		align-items: center;
		justify-content: center;
		
		transition: all 0.3s;
		z-index: 9;
		
		&.show {
			top: 100rpx;
			left: 0;
		}
		
		.items-bar {
			width: 100%;
			height: 50rpx;
			color: #fff;
			border-radius: 10rpx;
			overflow: hidden;
			background-color: #000;
			
			display: flex;
			flex-direction: row;
			align-items: center;
			justify-content: center;
			
			.select-item {
				flex: 1;
				height: 50rpx;
				line-height: 50rpx;
				text-align: center;
				font-size: 24rpx;
				transition: all 0.3s;
				
				&.select-it {
					background-color: darkorange;
				}
			}
		}
	}
	.photo-watermark-area {
		width: 750rpx;
		background-color: #fff;
		position: fixed;
		left: 0;
		bottom: 300rpx;
		color: #000000;
		font-size: 24rpx;
		
		.content {
			width: 710rpx;
			padding: 10rpx 20rpx;
			
			display: flex;
			flex-direction: row;
			flex-wrap: wrap;
			
			.item {
				width: 355rpx;
				
				display: flex;
				flex-direction: column;
				justify-content: center;
				align-items: flex-start;
			}
		}
	}
	.take-photo-mask {
		width: 100vw;
		height: calc(100vh - 400rpx);
		background-color: #000000;
		position: fixed;
		z-index: 100;
		top: 100rpx;
		bottom: 300rpx;
		left: 0;
	}
}

@keyframes takephoto {
	0% {
		transform: scale(1);
	}
	50% {
		transform: scale(0.8);
	}
	100% {
		transform: scale(1);
	}
}

@keyframes reversing {
	0% {
		transform: rotate(0deg);
	}
	100% {
		transform: rotate(180deg);
	}
}
</style>
