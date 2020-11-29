<template>
	<view>
		<image :src="fisherDetail.backgroud_img" class="image" mode="widthFix"></image>
		<view class="default-window white flex" style="align-items: flex-start;">
			<image :src="fisherDetail.headimgurl" style="width: 100rpx;height: 100rpx;display: block;border-radius: 50%;" mode="widthFix"></image>
			<view class="name-window">
				<view class="bold">{{fisherDetail.nickname}}</view>
				<view>
					<u-tag v-if="fisherDetail.is_subscribe==1" text="已认证" type="success" mode="dark"></u-tag>
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
		<view v-if="current==3">
			<view class="flex white u-text-center default-window">
				<navigator class="integral-item">
					<view class="value">{{fisherDetail.love}}</view>
					<view class="title">爱心</view>
				</navigator>
				<navigator class="integral-item">
					<view class="value">{{fisherDetail.popularity}}</view>
					<view class="title">人气</view>
				</navigator>
				<navigator class="integral-item">
					<view class="value">{{fisherDetail.subscribes}}</view>
					<view class="title">渔夫</view>
				</navigator>
			</view>
			<view class="default-window flex place white">
				<view>账号主体</view>
				<view>{{fisherDetail.company_name}}</view>
			</view>
			<view class="default-window flex place white">
				<view>客服</view>
				<view>{{fisherDetail.service_phone}}</view>
			</view>
			<view class="default-window white">
				<view>所在地</view>
				<view class="u-font-12 u-tips-color">{{fisherDetail.company_address}}</view>
			</view>
			<view class="default-window white">
				<view>渔夫号简介</view>
				<view class="u-font-12 u-tips-color">{{fisherDetail.introduction}}</view>
			</view>
		</view>
		<view v-if="current==4">
			<product-list :productList="productList"></product-list>
			<view class="default-window" v-if="productList.length==0">
				<u-empty text="暂无商品"></u-empty>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list: [{
					name: '首页',
				}, {
					name: '活动'
				}, {
					name: '资讯',
				}, {
					name: '资料',
				}, {
					name: '商品',
				}],
				current: 0,
				loadId: 0,

				fisherCover: [], //渔夫号轮播
				fisherDetail: {}, //渔夫号详情
				activityList: [], //活动列表
				articleList: [], //资讯列表
				modalList: [], //模块图列表
				productList:[]
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
			if (data.sn) {
				this.$store.commit('setSn', {
					ref_sn: data.sn
				})
			} else {
				console.log('无sn')
			}
		},
		onReady() {
			this.loadFisherDetail();
			this.loadCarousel();
			this.loadModal();
			this.loadArticle();
			this.loadActivity();
			this.loadProduct();
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
			//加载商品
			loadProduct() {
			    let params = {
			        fisher_id: this.loadId,
			    };
			    this.$api('Product/lists', params).then(data => {
			        if (data.status == 1) {
			            this.productList = data.data.product_list;
			        } else {
			            this.$showModal(data.msg);
			        }
			    });
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
							title: this.fisherDetail.nickname
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

	.integral-item {
		flex: 1;

		.value {
			// font-weight: bold;
			font-size: 36rpx;
			line-height: 1.5;
		}

		.title {
			font-size: 24rpx;

		}
	}
</style>
