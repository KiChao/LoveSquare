<template>
	<view>
		<view class="user-window white flex place">
			<view class="flex">
				<view class="head-window">
					<image :src="userInfo.headimgurl||'/static/logo.png'" class="head" mode="aspectFill"></image>
				</view>
				<view v-if="loginStatus" class="nickname-window">
					<text>{{userInfo.nickname}}</text>
					<view>
						<level-tag :level="userInfo.lv"></level-tag>
					</view>
				</view>
				<navigator v-else url="/pages/public/login" hover-class="none" class="nickname-window">
					请登录
				</navigator>
			</view>

			<view v-if="loginStatus" class="flex">
				<view style="position: relative;">
					<u-icon @click="sign" size="50" name="fingerprint" color="#333333"></u-icon>
					<view class="u-font-10 u-text-center">签到</view>
				</view>
				<navigator url="/pages/usercenter/systemMessage/systemMessage" hover-class="none" style="position: relative;margin-left: 20rpx;">
					<u-icon size="50" name="email" color="#333333"></u-icon>
					<view class="u-font-10 u-text-center">信息</view>
					<u-badge :offset="[-10,-10]" size="mini" type="error" :count="waitRead"></u-badge>
				</navigator>

			</view>
		</view>
		<view v-if="loginStatus" class="flex white u-text-center">
			<navigator hover-class="none" url="/pages/usercenter/integralRecord/integralRecord?type=0" class="integral-item">
				<view class="value">{{userInfo.fish}}</view>
				<view class="title">鱼仔</view>
			</navigator>
			<navigator hover-class="none" url="/pages/usercenter/integralRecord/integralRecord?type=1" class="integral-item">
				<view class="value">{{userInfo.love}}</view>
				<view class="title">爱心</view>
			</navigator>
			<navigator hover-class="none" url="/pages/usercenter/integralRecord/integralRecord?type=2" class="integral-item">
				<view class="value">{{userInfo.red}}</view>
				<view class="title">红心</view>
			</navigator>
			<navigator hover-class="none" url="/pages/usercenter/customer/customer" class="btn-item">
				<view class="sign-btn">
					联系客服
				</view>
			</navigator>
		</view>
		<view v-if="loginStatus" class="card">
			<view class="default-window bold">
				活动记录
			</view>
			<view class="flex around order-window">
				<navigator hover-class="none" url="/pages/usercenter/activityOrder/activityOrder?type=0" class="default-window">
					<view style="position: relative;">
						<image src="/static/usercenter/daishenhe.jpg" class="img" mode="aspectFill"></image>
						<u-badge :count="activity_form_count[0]" :offset="[-10, -10]"></u-badge>
					</view>
					<view class="title">待审核</view>
				</navigator>
				<navigator hover-class="none" url="/pages/usercenter/activityOrder/activityOrder?type=1" class="default-window">
					<view style="position: relative;">
						<image src="/static/usercenter/daiqiandao.jpg" class="img" mode="aspectFill"></image>
						<u-badge :count="activity_form_count[1]" :offset="[-10, -10]"></u-badge>
					</view>
					<view class="title">待签到</view>
				</navigator>
				<navigator hover-class="none" url="/pages/usercenter/activityOrder/activityOrder?type=2" class="default-window">
					<view style="position: relative;">
						<image src="/static/usercenter/daiqiantui.jpg" class="img" mode="aspectFill"></image>
						<u-badge :count="activity_form_count[2]" :offset="[-10, -10]"></u-badge>
					</view>
					<view class="title">待签退</view>
				</navigator>
				<navigator hover-class="none" url="/pages/usercenter/activityOrder/activityOrder?type=3" class="default-window">
					
					<view style="position: relative;">
						<image src="/static/usercenter/yiwancheng.jpg" class="img" mode="aspectFill"></image>
						<u-badge :count="activity_form_count[3]" :offset="[-10, -10]"></u-badge>
					</view>
					<view class="title">已完成</view>
				</navigator>

			</view>
		</view>
		<view v-if="loginStatus" class="card">
			<!-- <navigator hover-class="none" url="/pages/address/address" class="default-window u-border-bottom flex place">
				<view class="flex">
					<u-icon name="map"></u-icon>
					<view style="padding: 0 30rpx;">地址管理</view>
				</view>
				<view>
					<u-icon name="arrow-right"></u-icon>
				</view>
			</navigator> -->
			<navigator hover-class="none" url="/pages/usercenter/poster/poster" class="default-window u-border-bottom flex place">
				<view class="flex">
					<u-icon name="photo"></u-icon>
					<view style="padding: 0 30rpx;">分享海报</view>
				</view>
				<view>
					<u-icon name="arrow-right"></u-icon>
				</view>
			</navigator>
			<navigator hover-class="none" url="/pages/usercenter/rebOrder/rebOrder" class="default-window u-border-bottom flex place">
				<view class="flex">
					<u-icon name="file-text"></u-icon>
					<view style="padding: 0 30rpx;">测试订单</view>
				</view>
				<view>
					<u-icon name="arrow-right"></u-icon>
				</view>
			</navigator>
			<navigator hover-class="none" url="/pages/fisher/detail?loadId=101" class="default-window u-border-bottom flex place">
				<view class="flex">
					<u-icon name="heart"></u-icon>
					<view style="padding: 0 30rpx;">公益广场渔夫号</view>
				</view>
				<view>
					<u-icon name="arrow-right"></u-icon>
				</view>
			</navigator>
			<navigator hover-class="none" url="/pages/usercenter/aboutUs/aboutUs" class="default-window u-border-bottom flex place">
				<view class="flex">
					<u-icon name="error-circle"></u-icon>
					<view style="padding: 0 30rpx;">关于我们</view>
				</view>
				<view>
					<u-icon name="arrow-right"></u-icon>
				</view>
			</navigator>
			<navigator v-if="userInfo.celebrity_lv==0" url="/pages/public/bind1" hover-class="none" class="default-window u-border-bottom flex place">
				<view class="flex">
					<u-icon name="lock"></u-icon>
					<view style="padding: 0 30rpx;">绑定手机号码</view>
				</view>
				<view>
					<u-icon name="arrow-right"></u-icon>
				</view>
			</navigator>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				userInfo: {},
				waitRead: 0,
				loginStatus: false,
				activity_form_count: []
			};
		},
		onShow() {
			this.loadUserDetail();
		},
		methods: {
			//签到
			sign() {
				this.$showModal('是否进行签到？', () => {
					this.$api('Sign/sign_in').then(data => {
						if (data.status == 1) {
							this.$showToast(data.msg);
							this.loadUserDetail();
						} else {
							this.$showToast(data.msg);
						}
					});
				})
			},
			//加载个人资料
			loadUserDetail() {
				this.$api2('UserCenter/index').then(data => {
					if (data.status == 1) {
						this.userInfo = data.data.user_info;
						this.waitRead = data.data.wait_read_sysmsg;
						this.activity_form_count = data.data.activity_form_count;
						this.loginStatus = true;
						
					}
				});
			},
		}
	}
</script>

<style lang="scss">
	.order-window {
		text-align: center;

		.img {
			width: 100rpx;
			height: 100rpx;
			display: block;
		}

		.title {
			font-size: 24rpx;
			line-height: 1.5;
		}
	}

	.integral-item {
		width: 25%;
		padding: 20rpx;

		.value {
			// font-weight: bold;
			font-size: 36rpx;
			line-height: 1.5;
		}

		.title {
			font-size: 24rpx;

		}
	}

	.btn-item {
		width: 25%;
		padding: 20rpx;

		.sign-btn {
			width: 100%;
			padding: 20rpx 0;
			background: linear-gradient(to left, #fa3534, #dd6161);
			border-radius: 60rpx;
			color: #FFFFFF;
			font-size: 24rpx;
		}
	}


	.user-window {
		padding: 50rpx 40rpx;

		.head-window {
			width: 120rpx;
			height: 120rpx;
			box-shadow: 0rpx 0rpx 6rpx #808080;
			border-radius: 50%;
			box-sizing: border-box;

			.head {
				height: 120rpx;
				width: 120rpx;
				border-radius: 50%;
				display: block;
			}
		}

		.nickname-window {
			padding: 0 40rpx;
			font-weight: bold;
			font-size: 34rpx;
		}
	}
</style>
