<template>
	<view>
		<view v-for="(item,index) in rebOrder" :key="index" class="card default-window">
			<view class="flex" style="align-items: flex-start;align-items: center;">
				<view class="head-window">
					<image :src="item.fisher_info.headimgurl" class="head" mode="widthFix"></image>
				</view>
				<view style="flex: 1;padding:0 30rpx;">
					<view class="font-red">{{item.fisher_info.nickname}}</view>
					<view class="u-tips-color u-font-sm content">{{item.fisher_info.company_address}}</view>
				</view>
				<view>
					<image @click="call(item.fisher_info.service_phone)" src="/static/usercenter/dianhua.png"
						style="width: 70rpx;height: 70rpx;display: block;">
					</image>
				</view>
			</view>
			<u-gap height="16"></u-gap>
			<view class="flex" style="justify-content: flex-end;">
				<view v-if="item.status==1&&item.current_question_id==0" style="margin-left: 10rpx;">
					<u-button @click="startAnswer(item.reb_test_id)" size="mini" type="error">开始答题</u-button>
				</view>
				<view v-if="item.status==1&&item.current_question_id!=0" style="margin-left: 10rpx;">
					<u-button @click="goOn(item.reb_test_id)" size="mini" type="error">继续答题</u-button>
				</view>
				<view v-if="item.status==2" style="margin-left: 10rpx;">
					<u-button @click="checkResult(item.reb_test_id)" size="mini" type="error">查看结果</u-button>
				</view>
			</view>
		</view>

		<u-popup v-model="show" mode="center">
			<view class="default-window">
				<u-field v-model="name" label="姓名" placeholder="请填写您的姓名"></u-field>
				<u-gap height="16"></u-gap>
				<u-button shape="circle" @click="setName" type="error">开始答题</u-button>
			</view>
		</u-popup>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				rebOrder: [],
				show: false,
				name: '',
				reb_test_id: '',
			};
		},
		onShow() {
			this.loadOrder();
		},
		methods: {
			//查看结果
			checkResult(id){
				this.$api('RebTest/result',{
					reb_test_id: id
				}).then(data => {
					
					if (data.status == 1) {
						this.$showModal(data.data.result);
					} else {
						this.$showToast(data.msg);
					}
				})
			},
			//继续答题
			goOn(id) {
				uni.navigateTo({
					url: '/pages/usercenter/rebOrder/rebTest?loadId=' + id
				})
			},
			//拨打电话
			call(phone) {
				uni.makePhoneCall({
					phoneNumber: phone
				})
			},
			//加载测试订单
			loadOrder() {
				this.$api('RebTest/lists').then(data => {
					
					if (data.status == 1) {
						this.rebOrder = data.data.test_list;
					} else {
						this.$showToast(data.msg);
					}
				})
			},
			//开始第一次答题
			startAnswer(id) {
				this.show = true;
				this.reb_test_id = id;
			},
			//设置名字开始答题
			setName() {
				this.$api('RebTest/set_name', {
					name: this.name,
					reb_test_id: this.reb_test_id
				}).then(data => {
					if (data.status == 1) {
						this.show = false;
						this.$showToast(data.msg);
						uni.navigateTo({
							url: '/pages/usercenter/rebOrder/rebTest?loadId=' + this.reb_test_id
						})
					} else {
						this.$showToast(data.msg);
					}
				})
			}
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
</style>
