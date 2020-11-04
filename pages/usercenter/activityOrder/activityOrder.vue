<template>
	<view>
		<u-sticky>
			<u-tabs active-color="#fa3534" :list="list" :is-scroll="false" :current="current" @change="change"></u-tabs>
		</u-sticky>
		<view v-if="current==0">
			<view v-if="activityOrder0.length==0" class="u-padding-60">
				<u-empty></u-empty>
			</view>
			<view v-for="(item,index) in activityOrder0" :key="index" class="card">
				<image :src="item.activity_info.main_cover" style="width: 100%;height: 400rpx;display: block;border-top-left-radius: 16rpx;border-top-right-radius: 16rpx;"
				 mode="aspectFill"></image>
				<view class="default-window">
					<view>{{item.activity_info.title}}</view>
					<view>类型：{{item.type_text}}</view>
					<view>时间：{{item.time}}</view>
				</view>
			</view>
		</view>
		<view v-if="current==1">
			<view v-if="activityOrder1.length==0" class="u-padding-60">
				<u-empty></u-empty>
			</view>
			<view v-for="(item,index) in activityOrder1" :key="index" class="card">
				<image :src="item.activity_info.main_cover" style="width: 100%;height: 400rpx;display: block;border-top-left-radius: 16rpx;border-top-right-radius: 16rpx;"
				 mode="aspectFill"></image>
				<view class="default-window">
					<view>{{item.activity_info.title}}</view>
					<view>类型：{{item.type_text}}</view>
					<view>时间：{{item.time}}</view>
				</view>
			</view>
		</view>
		<view v-if="current==2">
			<view v-if="activityOrder2.length==0" class="u-padding-60">
				<u-empty></u-empty>
			</view>
			<view v-for="(item,index) in activityOrder2" :key="index" class="card">
				<image :src="item.activity_info.main_cover" style="width: 100%;height: 400rpx;display: block;border-top-left-radius: 16rpx;border-top-right-radius: 16rpx;"
				 mode="aspectFill"></image>
				<view class="default-window">
					<view>{{item.activity_info.title}}</view>
					<view>类型：{{item.type_text}}</view>
					<view>时间：{{item.time}}</view>
				</view>
			</view>
		</view>
		<view v-if="current==3">
			<view v-if="activityOrder3.length==0" class="u-padding-60">
				<u-empty></u-empty>
			</view>
			<view v-for="(item,index) in activityOrder3" :key="index" class="card">
				<image :src="item.activity_info.main_cover" style="width: 100%;height: 400rpx;display: block;border-top-left-radius: 16rpx;border-top-right-radius: 16rpx;"
				 mode="aspectFill"></image>
				<view class="default-window">
					<view>{{item.activity_info.title}}</view>
					<view>类型：{{item.type_text}}</view>
					<view>时间：{{item.time}}</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list: [{
					name: '待审核'
				}, {
					name: '待签到'
				}, {
					name: '待签退'
				}, {
					name: '已完成'
				}],
				current: 0,
				activityOrder0: [],
				activityOrder1: [],
				activityOrder2: [],
				activityOrder3: [],
			};
		},
		onLoad(data) {
			this.current = data.type || 0
		},
		onReady() {
			this.loadOrder();
		},
		methods: {
			change(index) {
				this.current = index;
			},
			//加载我预约的活动
			loadOrder() {
				this.$api('Activity/my_join').then(data => {

					if (data.status == 1) {
						this.activityOrder0 = [];
						this.activityOrder1 = [];
						this.activityOrder2 = [];
						this.activityOrder3 = [];
						let orderList = data.data.activity_form_list;
						for (let m in orderList) {
							if (orderList[m].status == 0) {
								this.activityOrder0.push(orderList[m]);

							} else if (orderList[m].status == 1) {
								this.activityOrder1.push(orderList[m]);

							} else if (orderList[m].status == 2) {
								this.activityOrder2.push(orderList[m]);

							} else {
								this.activityOrder3.push(orderList[m]);

							}
						}
					} else {
						this.$showToast(data.msg);
					}
				});
			},
		}
	}
</script>

<style lang="scss">

</style>
