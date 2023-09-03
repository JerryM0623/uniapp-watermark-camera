<template>
	<view class="main-container">
		<wxml-to-canvas class="widget"></wxml-to-canvas>
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
			 const tempFilePath = options.tempFilePath
			 if (!tempFilePath) {
				 uni.showModal({
				 	title: '注意',
				 	content: '图像渲染错误，请重新拍摄！',
				 	showCancel: false,
					confirmColor: "#dd524d",
				 	success: res => {
						// 返回上一层
						uni.navigateBack()
					},
				 });
			 } else {
				 this.tempFilePath = tempFilePath
			 }
		},
		data() {
			return {
				// canvas 渲染层
				widget: null,
				// 图像文件缓存链接
				tempFilePath: ''
			};
		}
	}
</script>

<style lang="less">

</style>
