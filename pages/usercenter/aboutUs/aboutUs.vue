<template>
	<view>
		<view class="u-padding-80 flex around">
			<view style="width: 200rpx;height: 200rpx;">
				<image class="image" src="/static/logo.png" mode="widthFix"></image>
				<view class="u-text-center default-window">
					<view>公益广场</view>
					<view class="u-font-12 u-tips-color">v1.0.0</view>
				</view>
			</view>
			
		</view>
		<navigator hover-class="none" url="/pages/fisher/detail?loadId=21" class="footer flex around default-window u-font-12">
			<view><text class="font-red">得人渔夫</text>提供技术支持 / 版权所有 © 2018-2020</view>
		</navigator>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			};
		},
		onReady() {
			
		}
	}
</script>

<style lang="scss">
	.footer{
		position: fixed;
		bottom: 0;
		width: 100%;
	}
</style>
