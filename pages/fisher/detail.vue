<template>
	<view>
		<image :src="fisherDetail.backgroud_img" class="image" mode="widthFix"></image>
		<view class="default-window white flex" style="align-items: flex-start;">
			<image :src="fisherDetail.headimgurl" style="width: 100rpx;height: 100rpx;display: block;border-radius: 50%;" mode="widthFix"></image>
			<view class="name-window">
				<view class="bold">{{fisherDetail.nickname}}</view>
				<view class="u-font-sm u-tips-color">
					{{fisherDetail.introduction}}
				</view>
			</view> 
		</view>
		<u-sticky>
			<view class="default-window white">
				<u-subsection @change="change" active-color="#FFFFFF" buttonColor="#fa3534" :list="list" :current="current"></u-subsection>
			</view>
		</u-sticky>
		<view v-if="current==0">
			<swiper autoplay>
				<swiper-item @click="jumpLink(item.href_type,item.href_value)" v-for="(item,index) in fisherCover" :key="index">
					<image :src="item.img_url" mode="widthFix" class="image"></image>
				</swiper-item>
			</swiper>
			<view class="modal-window">
				<view v-for="(item, index) in modalList" :key="index" @click="jumpLink(item.href_type,item.href_value)" :style="{ width: `${item.width}%` }">
					<image :src="item.img_url" class="image" mode="widthFix"></image>
				</view>
			</view>
		</view>
		<view v-if="current==1">
			<activity-list :loadlist="activityList"></activity-list>
			<view class="default-window" v-if="activityList.length==0">
				<u-empty text="暂无活动"></u-empty>
			</view>
		</view>
		<view v-if="current==2">
			<article-list :loadlist="articleList"></article-list>
			<view class="default-window" v-if="articleList.length==0">
				<u-empty text="暂无资讯"></u-empty>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list: [{
					name: '首页'
				}, {
					name: '活动'
				}, {
					name: '资讯',
				}],
				current: 0,
				loadId: 0,

				fisherCover: [], //渔夫号轮播
				fisherDetail: {}, //渔夫号详情
				activityList: [], //活动列表
				articleList: [], //资讯列表
				modalList: [], //模块图列表
			};
		},
		onShareAppMessage() {
			return {
				title: this.fisherDetail.nickname,
				path: `/pages/fisher/detail?loadId=${this.fisherDetail.fisher_id}&&sn=${uni.getStorageSync('sn')}`,
				imageUrl: this.fisherDetail.backgroud_img,
			}
		},
		onLoad(data) {
			this.loadId = data.loadId;
			this.$store.commit('setSn', {
				ref_sn: data.sn
			})
		},
		onReady() {
			this.loadFisherDetail();
			this.loadCarousel();
			this.loadModal();
			this.loadArticle();
			this.loadActivity();
		},
		methods: {
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
			change(index) {
				this.current = index;
			},
			//加载渔夫号模块
			loadModal() {
				let params = {
					fisher_id: this.loadId,
				};
				this.$api('Fisher/module', params).then(data => {
					if (data.status == 1) {
						this.modalList = data.data.module_list;
					} else {
						this.$showModal(data.msg);
					}
				});
			},
			//加载渔夫号轮播
			loadCarousel() {
				let params = {
					fisher_id: this.loadId,
				};
				this.$api('Fisher/cover', params).then(data => {
					if (data.status == 1) {
						this.fisherCover = data.data.cover_list;
					} else {
						this.$showModal(data.msg);
					}
				});
			},
			//加载渔夫号详情
			loadFisherDetail() {
				let params = {
					fisher_id: this.loadId,
				};
				this.$api('Fisher/detail', params).then(data => {
					if (data.status == 1) {
						this.fisherDetail = data.data.fisher;
						uni.setNavigationBarTitle({
							title:this.fisherDetail.nickname
						})
					} else {
						this.$showModal(data.msg);
					}
				});
			},
			//加载渔夫号活动
			loadActivity() {
				let params = {
					fisher_id: this.loadId,
				};
				this.$api('Activity/index', params).then(data => {
					if (data.status == 1) {
						this.activityList = data.data.activity_list;
					} else {
						this.$showModal(data.msg);
					}
				});
			},
			//加载渔夫号资讯
			loadArticle() {
				let params = {
					fisher_id: this.loadId,
				};
				this.$api('Article/index', params).then(data => {
					if (data.status == 1) {
						this.articleList = data.data.article_list;
					} else {
						this.$showModal(data.msg);
					}
				});
			}
		}
	}
</script>

<style lang="scss">
	.name-window {
		padding-left: 30rpx;
		flex: 1
	}

	.modal-window {
		display: flex;
		flex-wrap: wrap;
		justify-content: flex-start;
	}
</style>
