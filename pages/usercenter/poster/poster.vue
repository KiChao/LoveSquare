<template>
	<view>
		<image @click="check" class="image" mode="widthFix" :src="poster"></image>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				poster: ''
			};
		},
		onReady() {
			this.getPoster();
		},
		methods: {
			getPoster() {
				let params = {
					type: 2
				}
				this.$api('UserCenter/mini_qrcode', params).then(data => {
					if (data.status == 1) {
						this.poster = data.data.qrcode;
					} else {
						this.$showToast(data.msg);
					}
				});
			},
			check() {
				// 预览图片
				let that = this;
				uni.previewImage({
					urls: [that.poster],

				});
			}
		}
	}
</script>

<style lang="scss">

</style>
