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
				<view class="icon">
					<image class="lighting" src="../../static/watermark-camera/top-tool-var/icon-lighting.svg"></image>
				</view>
				<view class="icon">
					<image class="reverse" src="../../static/watermark-camera/top-tool-var/icon-reverse.svg" mode=""></image>
				</view>
			</view>
		</view>
		<view class="middle-camera-review">
			<camera class="watermark-camera" device-position="back" flash="off" @error="error"></camera>
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
	</view>
</template>

<script>
	export default {
		onLoad(){
			console.log("水印相机页面已打开")
		},
		data() {
			return {
				isClick: false,
				Timer: null
			}
		},
		methods: {
			takePhoto() {
				this.isClick = true
				if (!this.timer) {
					this.timer = setTimeout(() => {
						this.isClick = false
						this.timer = null
					},200)
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
</style>
