<template>
	<view>
		<navigator v-for="(item,index) in activityList" :key="index" hover-class="none" :url="`/pages/activity/detail?loadId=${item.activity_id}`" style="align-items: flex-start;" class="u-border-bottom white flex default-window">
			<view>
				<image :src="item.main_cover" mode="aspectFill" class="activity-image"></image>
			</view>
			<view style="flex: 1;padding-left: 30rpx;">
				<view class="bold">{{item.title}}</view>
				<view class="u-font-sm">
					报名人数：<text class="bold font-red">{{item.current_join_num}}</text>人
				</view>
				<view class="flex place u-tips-color u-font-sm">
					<view>
						{{item.time}}
					</view>
					<view>
						{{item.page_views}}人看过
					</view>
				</view>
			</view>
		</navigator>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				activityList: []
			};
		},
		props: {
			loadlist: {
				type: Array,
			}
		},
		watch: {
			loadlist() {
				this.activityList = this.loadlist;
			}
		},
		mounted() {
			this.activityList = this.loadlist;
		}
	}
</script>

<style lang="scss">
	.activity-image {
		width: 160rpx;
		height: 160rpx;
		display: block;
		border-radius: 16rpx;
	}
</style>
