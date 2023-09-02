<template>
	<view class="main-container">
		<view class="top-tools-bar">
			<view class="left">
				<view class="icon">
					<image class="setting" src="../../static/watermark-camera/top-tool-var/icon-setting.svg"></image>
				</view>
			</view>
			<view class="right">
				<view class="icon">
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
			<camera class="watermark-camera" :device-position="isBack ? 'back' : 'front'" 
				:flash="lightingSettingValue" @error="error"></camera>
		</view>
		<view class="bottom-tools-bar">
			<view class="tools-content">
				<view class="tool photo-album">
					<view class="icon thumbnail"></view>
					<view class="text">相册</view>
				</view>
				<view class="tool photo-location">
					<image class="icon location" src="../../static/watermark-camera/bottom-tool-bar/icon-location.svg"></image>
					<view class="text">地址</view>
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
	</view>
</template>

<script>
	export default {
		onLoad(){
			console.log("水印相机页面已打开")
		},
		data() {
			return {
				// 用于界面UI变动
				isClick: false,
				isReversing: false,
				isShowLighting: false,
				lightingSettingIndex: '0',
				takePhotoTimer: null,
				reverseTimer: null,
				
				// 是否启用后置摄像头
				isBack: true,
				// 存储设置的闪光灯设置
				lightingSettingValue: "auto"
			}
		},
		methods: {
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
					this.takePhotoTimer = setTimeout(() => {
						this.isClick = false
						this.takePhotoTimer = null
					},200)
				}
			},
			handleReverse() {
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
				// 开启/关闭 选择窗
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
		// background-color: pink;
		
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
		width: 710rpx;
		height: 100rpx;
		padding: 0 20rpx;
		background-color: rgba(0, 0, 0, 0.7);
		border-bottom-left-radius: 10rpx;
		border-bottom-right-radius: 10rpx;
		position: fixed;
		top: -110rpx;
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
