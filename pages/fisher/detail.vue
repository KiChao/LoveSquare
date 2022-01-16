<template>
	<view>
		<image :src="fisherDetail.backgroud_img" class="image" mode="widthFix"></image>
		<view class="default-window white flex" style="align-items: flex-start;">
			<image :src="fisherDetail.headimgurl"
				style="width: 100rpx;height: 100rpx;display: block;border-radius: 50%;" mode="widthFix"></image>
			<view class="name-window">
				<view class="bold">{{fisherDetail.nickname}}</view>
				<view>
					<u-tag v-if="fisherDetail.is_subscribe==1" text="已认证" type="success" mode="dark"></u-tag>
				</view>
			</view>
		</view>
		<u-sticky>
			<view class="white" style="padding: 30rpx;">
				<view class="flex around tabs">
					<view @click="current='index'" :class="{'tabs-active':current=='index'}" class="tabs-item">首页</view>
					<view @click="current='detail'" :class="{'tabs-active':current=='detail'}" class="tabs-item">资料</view>
					<view @click="current=item" :class="{'tabs-active':current==item}" class="tabs-item"
						v-for="(item,index) in tabsData" :key="index">{{tabsText[item]}}</view>
				</view>
			</view>
		</u-sticky>


		<view v-if="current=='index'">
			<swiper autoplay>
				<swiper-item @click="jumpLink(item.href_type,item.href_value)" v-for="(item,index) in fisherCover"
					:key="index">
					<image :src="item.img_url" mode="widthFix" class="image"></image>
				</swiper-item>
			</swiper>
			<view class="modal-window">
				<view v-for="(item, index) in modalList" :key="index" @click="jumpLink(item.href_type,item.href_value)"
					:style="{ width: `${item.width}%` }">
					<image :src="item.img_url" class="image" mode="widthFix"></image>
				</view>
			</view>
		</view>
		<view v-if="current=='activity'">
			<activity-list :loadlist="activityList"></activity-list>
			<view class="default-window" v-if="activityList.length==0">
				<u-empty text="暂无活动"></u-empty>
			</view>
		</view>
		<view v-if="current=='article'">
			<article-list :loadlist="articleList"></article-list>
			<view class="default-window" v-if="articleList.length==0">
				<u-empty text="暂无资讯"></u-empty>
			</view>
		</view>
		<view v-if="current=='detail'">
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
		<view v-if="current=='product'">
			<product-list :productList="productList"></product-list>
			<view class="default-window" v-if="productList.length==0">
				<u-empty text="暂无商品"></u-empty>
			</view>
		</view>
		<view v-if="current=='reb'">
			<view class="card" v-for="(item,index) in rebList" :key="index">
				<image :src="item.image" mode="aspectFill" class="article-image"></image>
				<view class="default-window">
					<view class="bold">{{item.name}}</view>
					<view class="flex place font-red u-font-sm">
						<view>
							￥{{item.price}}
						</view>
						<view>
							<u-button @click="buyReb(item.reb_id)" size="mini" type="error">购买</u-button>
						</view>
					</view>
				</view>
			</view>
			<view class="default-window" v-if="rebList.length==0">
				<u-empty text="暂无测试套餐"></u-empty>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				current: 'index',
				loadId: 0,
				tabsData: [],
				tabsText: {
					product: '商品',
					activity: '活动',
					article: '文章',
					'reb': '测试'
				},

				fisherCover: [], //渔夫号轮播
				fisherDetail: {}, //渔夫号详情
				activityList: [], //活动列表
				articleList: [], //资讯列表
				modalList: [], //模块图列表
				productList: [],
				rebList: [], //测试列表
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
			let query = this.$getRequestParameters(decodeURIComponent(data.scene))
			let sn = query.sn;
			if (sn) {
				this.$store.commit('setSn', {
					ref_sn: sn
				})
			} else {
				console.log('无sn')
			}
			let id = query.loadId;
			if (id) {
				this.loadId = id;
			} else {
				console.log('无sn')
			}
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

			this.loadReb();
		},
		methods: {
			//购买套餐
			buyReb(id) {
				this.$showModal('是否购买该套餐？', () => {
					this.$api('RebTest/create', {
						reb_id: id,
						fisher_id:this.fisherDetail.fisher_id
					}).then(data => {
						if (data.status == 1) {
							this.$api('Pay/reb_test_pay',{
								no:data.data.no
							}).then(data=>{
								if (data.status == 1) {
									this.$pay(data.data.response).then(data=>{
										uni.navigateTo({
											url:'/pages/usercenter/rebOrder/rebOrder'
										})
									}).catch((res)=>{
										this.$showToast(JSON.stringify(res))
									})
								} else {
									this.$showToast(data.msg);
								}
							})
						} else {
							this.$showToast(data.msg);
						}
					})
				})
			},
			//加载套餐列表
			loadReb() {
				this.$api('RebTest/index').then(data => {
					if (data.status == 1) {
						this.rebList = data.data.reb;
					} else {
						this.$showModal(data.msg);
					}
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
						this.tabsData = this.fisherDetail.product_priv_str
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

	.tabs {
		background-color: #F8F8F8;
		border-radius: 8rpx;
		padding: 10rpx;

		.tabs-item {
			flex: 1;

			border-radius: 8rpx;
			padding: 16rpx;
			text-align: center;
		}

		.tabs-active {
			background-color: #fa3534;
			color: #FFFFFF;
		}
	}

	.article-image {
		width: 100%;
		height: 300rpx;
		display: block;
		border-top-left-radius: 16rpx;
		border-top-right-radius: 16rpx;
	}
</style>
