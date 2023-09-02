<template>
	<view class="content">
		<button type="primary" @click="openCamera">点击打开相机界面</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: 'Hello'
			}
		},
		onLoad() {

		},
		methods: {
			openCamera(){
				uni.navigateTo({
					url: '/pages/watermark-camera/watermark-camera'
				})
			}
		},
	}
</script>

<style>
	.content {
		width: 750rpx;
		height: 100vh;
		
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}
</style>
