<template>
	<view>
		<swiper style="height: 56vw;" autoplay>
			<swiper-item @click="jumpLink(item.href_type,item.href_value,item.href_path)"
				v-for="(item,index) in carouselList" :key="index">
				<image :src="item.img_url" class="image" mode="widthFix"></image>
			</swiper-item>
		</swiper>
		<view>
			<navigator hover-class="none" :url="`/pages/fisher/detail?loadId=${item.fisher_id}`"
				v-for="(item,index) in fisherList" :key="index" class="card flex default-window"
				style="align-items: flex-start;">
				<view class="head-window">
					<image :src="item.headimgurl" class="head" mode="widthFix"></image>
				</view>
				<view style="flex: 1;padding-left: 30rpx;">
					<view class="font-red">{{item.nickname}}</view>
					<view class="u-tips-color u-font-sm content">{{item.introduction}}</view>
				</view>
			</navigator>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				carouselList: [],
				fisherList: [],
				page: 0
			};
		},
		onReady() {
			this.loadCarousel();
			this.loadFisherList();
		},
		onReachBottom() {
			this.loadFisherList();
		},
		methods: {
			jumpLink(type, value, path = 'pages/index/index') {
				switch (type) {

					case 'article':
						uni.navigateTo({
							url: '/pages/article/detail?loadId=' + value
						})
						break;
					case 'activity':
						uni.navigateTo({
							url: '/pages/activity/detail?loadId=' + value
						})
						break;
					case 'miniProgram':
						wx.navigateToMiniProgram({
							appId: value,
							path: path,
						})
						break;
					case 'other':
						uni.navigateTo({
							url: value
						})
						break;
					default:

						break;
				}
			},
			//加载轮播
			loadCarousel() {
				let params = {
					type: 'gygc_index'
				};
				this.$api('Carousel/lists', params).then(data => {
					if (data.status == 1) {

						this.carouselList = data.data.carousel_list;
					} else {
						this.$showToast(data.msg);
					}
				});
			},
			//加载公益组织列表
			loadFisherList() {
				this.page = this.page + 1;
				this.$api('GYGCFisher/index',{
					page:this.page
				}).then(data => {
					let list=data.data.fisher_list;
					list.forEach(item=>{
						this.fisherList.push(item)
					})
					// this.fisherList = 
				});
			},
		}
	}
</script>

<style lang="scss">
	.head-window {
		width: 120rpx;
		height: 120rpx;

		.head {
			width: 120rpx;
			height: 120rpx;
			display: block;
			border-radius: 50%;
		}
	}

	.content {
		overflow: hidden;
		text-overflow: ellipsis;
		display: -webkit-box;
		-webkit-line-clamp: 2; //多行在这里修改数字即可
		overflow: hidden;
		/* autoprefixer: ignore next */
		-webkit-box-orient: vertical;
	}
</style>
