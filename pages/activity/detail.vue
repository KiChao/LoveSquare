<template>
	<view>
		<image :src="activityDetail.main_cover" class="image" mode="widthFix"></image>
		<view class="default-window white">
			<view class="bold">
				{{activityDetail.title}}
			</view>
			<view class="u-font-sm u-tips-color">
				{{activityDetail.subtitle}}
			</view>
		</view>
		<u-gap height="16"></u-gap>
		<view class="default-window white u-border-bottom">
			<view>报名人数</view>
			<view class="u-font-sm u-tips-color">{{activityDetail.current_join_num}}人</view>
		</view>
		<view class="default-window white u-border-bottom">
			<view>活动开始时间</view>
			<view class="u-font-sm u-tips-color">{{activityDetail.b_start_time}} 至 {{activityDetail.b_end_time}}</view>
		</view>
		<view class="default-window white u-border-bottom">
			<view>报名时间</view>
			<view class="u-font-sm u-tips-color">{{activityDetail.a_start_time}} 至 {{activityDetail.a_end_time}}</view>
		</view>
		<navigator :url="`/pages/fisher/detail?loadId=${activityDetail.fisher_id}`" hover-class="none" class="card flex default-window">
			<image :src="fisherDetail.headimgurl" mode="aspectFill" class="fisher-head"></image>
			<view class="name-window">
				<view>{{fisherDetail.nickname}}</view>
				<view class="u-tips-color u-font-sm">
					发布于：{{activityDetail.time}}
				</view>
			</view>
		</navigator>
		<view class="white default-window">
			<u-parse :html="activityDetail.content" :lazy-load="true" :show-with-animation="true" :selectable="true"></u-parse>
		</view>
		<u-gap height="120"></u-gap>
		<view class="bottom-btn default-window white">
			<u-button @click="show=true" type="error">报名</u-button>
		</view>
		<u-popup closeable border-radius="16" mode="bottom" v-model="show">
			<view class="default-window u-text-center bold">
				报名表
			</view>
			<view v-if="activityDetail.person_type==1" class="default-window">
				<view class="u-font-sm u-tips-color">您的身份</view>
				<radio-group @change="choose">
					<view style="padding: 20rpx 0;" class="flex place">
						<view>志愿者(剩余{{activityDetail.recruit_count}}人)</view>
						<view>
							<radio value="1" :disabled="activityDetail.recruit_count==0"></radio>
						</view>
					</view>
					<view style="padding: 20rpx 0;" class="flex place">
						<view>受益人(剩余{{activityDetail.service_count}}人)</view>
						<view>
							<radio value="2" :disabled="activityDetail.service_count==0"></radio>
						</view>
					</view>
				</radio-group>
			</view>
			<view v-else>
				<view class="flex place default-window">
					<view>参与人剩余（{{activityDetail.join_count}}人）</view>
				</view>
			</view>
			<view class="default-window">
				<view class="u-font-sm u-tips-color">您的信息</view>
				<u-field v-model="name" required="" label="姓名" placeholder="请填写您的姓名">
				</u-field>
				<u-field v-model="phone" type="number" required="" label="手机" placeholder="请填写您的手机号码">
				</u-field>
			</view>
			<view class="default-window">
				<view class="u-font-sm u-tips-color">其他信息</view>
				<u-field v-model="remark" required="" label="备注" placeholder="报名时附加信息">
				</u-field>
			</view>
			<view class="default-window">
				<u-button @click="applyActivity" type="error">报名</u-button>
			</view>
		</u-popup>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				loadId: null,
				show: false,
				activityDetail: {},
				fisherDetail: {},
				name: '',
				phone: '',
				remark: '',
				radio: 0,
			};
		},
		onShareAppMessage() {
			return {
				title: this.activityDetail.title,
				path: `/pages/activity/detail?loadId=${this.activityDetail.activity_id}&&sn=${uni.getStorageSync('sn')}`,
				imageUrl: this.activityDetail.main_cover,
			}
		},
		onLoad(data) {
			this.loadId = data.loadId;
			this.$store.commit('setSn', {
				ref_sn: data.sn
			})
		},
		onReady() {
			this.loadActivity();
		},
		methods: {
			//报名活动
			applyActivity() {
				if (this.radio == 0 || this.name==''||this.phone=='') {
					this.$showToast('请完整填写申请表');
					return;
				}
				this.show = false;
				this.$showModal('是否报名该活动？', () => {
					let params = {
						activity_id: this.loadId,
						type: this.radio,
						name: this.name,
						phone: this.phone,
						remark: this.remark,
						// img: JSON.stringify(that.fileList)
					};
					this.$api('Activity/join_activity', params).then(data => {
						if (data.status == 1) {
							this.$showToast(data.msg);
							
						} else {
							this.$showToast(data.msg);
						}
					});
				})
			},
			//加载活动内容
			loadActivity() {
				let params = {
					activity_id: this.loadId
				};
				this.$api('Activity/detail', params).then(data => {
					if (data.status == 1) {
						this.activityDetail = data.data.activity;
						this.fisherDetail = data.data.fisher;
						uni.setNavigationBarTitle({
							title:this.activityDetail.title
						})
					} else {
						this.$showToast(data.msg);
					}
				});
			},
			//选择身份
			choose(e) {
				this.radio = e.detail.value;
			}
		}
	}
</script>

<style lang="scss">
	.fisher-head {
		width: 100rpx;
		height: 100rpx;
		display: block;
		border-radius: 50%;
	}

	.name-window {
		padding-left: 30rpx;
	}

	.bottom-btn {
		width: 100%;
		position: fixed;
		bottom: 0;
		z-index: 100;
	}
</style>
