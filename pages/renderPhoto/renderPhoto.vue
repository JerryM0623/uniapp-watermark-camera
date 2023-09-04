<template>
	<view class="main-container">
		<wxml-to-canvas class="widget" :width="canvasOption.width" :height="canvasOption.height"></wxml-to-canvas>

		<view class="btns-group">
			<button @click="backToCamera">重新拍摄</button>
			<button type="primary" @click="saveImage">保存图片</button>
		</view>
	</view>
</template>

<script>
	export default {
		onLoad(options) {
			const widget = this.selectComponent('.widget')
			if (widget) {
				this.widget = widget
			}

			// 获取携带参数
			const tempImagePath = options.tempImagePath
			if (!tempImagePath) {
				uni.showModal({
					title: '注意',
					content: '图像渲染错误，请重新拍摄！',
					showCancel: false,
					confirmColor: "#dd524d",
					success: res => {
						uni.navigateBack()
					},
				});
			} else {
				this.tempImagePath = tempImagePath
				console.log(this.tempImagePath)
				this.createWXML()
			}
			
			const { time, location, latitude, longitude } = options
			
		},
		onReady() {
				setTimeout(() => {
					this.calculatePhotoSize()
					this.calculateWatermarkSize()
					this.renderCanvas()
				}, 500)
		},
		data() {
			return {
				// canvas 渲染层
				widget: null,
				// 图像文件缓存链接
				tempImagePath: '',
				// canvas 配置
				canvasOption: {
					// 宽高单位 px
					width: 375,
					height: 600
				},
				watermarkData: {
					time: '',
					location: '',
					longitude: '',
					latitude: ''
				},
				// canvas 渲染内容
				wxml: '',
				// canvas 渲染样式
				style: {
					container: {
						// 宽高需要根据设备的屏幕实际尺寸进行计算,300仅仅用于占位
						width: 300,
						height: 300
					},
					tempPhoto: {
						// 宽高需要根据设备的屏幕实际尺寸进行计算,300仅仅用于占位
						width: 300,
						height: 300
					},
					photoWatermarkArea: {
						// 宽高需要根据设备的屏幕实际尺寸进行计算,300仅仅用于占位
						width: 300,
						height: 300,
						position: 'absolute',
						left: 0,
						bottom: 0,
						paddingLeft: 10,
						paddingRight: 10,
						paddingTop: 5,
						paddingBottom: 5,
						fontSize: 14,
						color: '#000000'
					},
					Content: {
						// 宽高需要根据设备的屏幕实际尺寸进行计算,300仅仅用于占位
						width: 300,
						height: 300,
						flexDirection: 'row',
						flexWrap: 'wrap'
					},
					item: {
						// 宽高需要根据设备的屏幕实际尺寸进行计算,300仅仅用于占位
						width: 300,
						height: 300,
						flexDirection: 'column',
						justifyContent: 'center',
						alignItems: 'flex-start'
					}
				}
			};
		},
		methods: {
			backToCamera() {
				uni.navigateBack()
			},
			saveImage() {
				console.log("保存图片")
				uni.authorize({
					scope: 'scope.writePhotosAlbum',
					fail: () => {
						uni.showModal({
							title: '注意',
							content: '未取得相册存储权限，无法保存照片',
							showCancel: false,
							confirmColor: "#dd524d",
							success: () => {},
						});
					}
				})
				// 格式有 jpg 和 png
				// 1 为质量最高，0.xxxx质量会变低
				this.widget.canvasToTempFilePath({
					fileType: 'jpg',
					quality: 1
				})
				.then((res) => {
					const {errMsg, tempFilePath} = res
					if (errMsg === 'canvasToTempFilePath:ok') {
						uni.saveImageToPhotosAlbum({
							filePath: tempFilePath,
							success: ({errMsg}) => {
								if (errMsg === 'saveImageToPhotosAlbum:ok') {
									uni.showModal({
										title: '注意',
										content: `图片保存完成!`,
										showCancel: false,
										success: () => {},
									})
								} else {
									uni.showModal({
										title: '注意',
										content: `图片保存失败!`,
										showCancel: false,
										confirmColor: "#dd524d",
										success: () => {},
									})
								}
							}
						})
					}
				})
				
			},
			createWXML() {
				// 生成 wxml
				this.wxml = `<view class="container" >
					<image class="temp-photo" src="${this.tempImagePath}"></image>
					<view class="photo-watermark-area">
						<view class="content">
							<view class="item time">时间：${this.watermarkData.time === '' ? '加载中...' : this.watermarkData.time}</view>
							<view class="item location">地点：${this.watermarkData.location === '' ? '请点击地点按钮手动输入...' : this.watermarkData.location}</view>
							<view class="item longitude">精度：${this.watermarkData.longitude === '' ? '正在获取中...' : this.watermarkData.longitude}</view>
							<view class="item latitude">纬度：${this.watermarkData.latitude === '' ? '正在获取中...' : this.watermarkData.latitude}</view>
						</view>
					</view>
				</view>`
				console.log(this.wxml)
			},
			renderCanvas() {
				// 渲染 canvas
				this.widget.renderToCanvas({
						wxml: this.wxml,
						style: this.style
					})
					.then(() => {})
					.catch(() => {})
			},
			calculatePhotoSize(){
				// 宽应该和屏幕宽度一致
				// 高度应该是 （100vh - 400rpx）之后在根据 dpr 比例转换成真实 px 值
				// 图像高度应该是和container一致
				const info = uni.getSystemInfoSync()
				const width = info.windowWidth
				const height = info.windowHeight
				const pixelRatio = info.pixelRatio
				console.log({
					width,
					height,
					pixelRatio
				})
				
				// 开始计算
				const realWidth = width
				const realHeight = Math.round(((height * pixelRatio) - 400) / pixelRatio);
				console.log({
					realWidth,
					realHeight
				})
				
				this.style.container.width = realWidth
				this.style.container.height = realHeight
				this.style.tempPhoto.width = realWidth
				this.style.tempPhoto.height = realHeight
			},
			calculateWatermarkSize(height) {
				console.log("计算水印尺寸")
			}
		}
	}
</script>

<style scoped lang="less">
	.main-container {
		width: 750rpx;
		height: 100vh;
		background-color: #000000;
		
		.btns-group {
			width: 750rpx;
			height: 200rpx;
			
			display: flex;
			flex-direction: row;
			align-items: center;
			justify-content: center;
		
			button {
				width: 300rpx;
		
				&:nth-child(1) {
					margin-right: 30rpx;
				}
		
				&:nth-child(2) {
					margin-left: 30rpx;
				}
			}
		}
	}
</style>