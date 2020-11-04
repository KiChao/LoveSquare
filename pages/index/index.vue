<template>
	<view>
		<u-sticky>
			<view class="white default-window search-window">
				<u-search :show-action="false" border-color="#fa3534" color="#333333" search-icon-color="#fa3534" bg-color="#FFFFFF"
				 placeholder="输入搜索内容" v-model="searchValue" @search="search"></u-search>
			</view>
		</u-sticky>
		<swiper autoplay>
			<swiper-item @click="jumpLink(item.href_type,item.href_value)" v-for="(item,index) in carouselList" :key="index">
				<image :src="item.img_url" class="image" mode="widthFix"></image>
			</swiper-item>
		</swiper>
		<view class="modal-window">
			<view @tap="linkJump(item.href_type,item.href_value)" v-for="(item,index) in modalList" :key="index" :style="{width: `${item.width}%`}">

				<u-image :fade="false" :src="item.img_url" width="100%" mode="widthFix"></u-image>
			</view>
		</view>
		<activity-list :loadlist="activityList"></activity-list>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				searchValue: '',
				carouselList: [],
				modalList: [],
				activityList: [],
				page: 0,
			}
		},
		onLoad(data) {
			if (data.scene) {
				let query = this.$getRequestParameters(decodeURIComponent(data.scene))
				let sn = query.sn;
				this.$store.commit('setSn', {
					ref_sn: sn
				})
			}
		},
		onReady() {
			this.loadCarousel();
			this.loadModal();
			this.loadActivity();
		},
		onReachBottom() {
			this.loadActivity();
		},
		methods: {
			search() {
				uni.navigateTo({
					url: '/pages/index/search?searchValue=' + this.searchValue + '&&type=0'
				})
			},
			jumpLink(type, value) {
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
					default:

						break;
				}
			},
			//加载活动
			loadActivity() {
				this.page = this.page + 1;
				let params = {
					page: this.page,
					type: 1
				}
				this.$api('Activity/index', params).then(data => {
					if (data.status == 1) {

						this.loadStatus = 'loading';
						let list = data.data.activity_list;
						if (list.length < 10) {
							this.loadStatus = 'nomore';
						}
						for (let m in list) {
							this.activityList.push(list[m]);
							this.loadStatus = 'loadmore';
						}
					} else {
						this.$showToast(data.msg);
					}
				});
			},
			//加载轮播
			loadCarousel() {
				let params = {
					type: 'jalp_index'
				};
				this.$api('Carousel/lists', params).then(data => {
					if (data.status == 1) {

						this.carouselList = data.data.carousel_list;
					} else {
						this.$showToast(data.msg);
					}
				});
			},
			//加载模块图
			loadModal() {
				let params = {
					type: "gygc_index"
				};
				this.$api('Module/lists', params).then(data => {
					if (data.status == 1) {
						this.modalList = data.data.module_list;
					} else {
						this.$showToast(data.msg);
					}
				});
			},
		}
	}
</script>

<style lang="scss">
	.modal-window {
		display: flex;
		flex-wrap: wrap;
		justify-content: flex-start;
	}
</style>
