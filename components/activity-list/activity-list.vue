<template>
	<view>
		<navigator v-for="(item,index) in activityList" :key="index" hover-class="none" :url="`/pages/activity/detail?loadId=${item.activity_id}`" class="card">
			<image :src="item.main_cover" mode="aspectFill" class="activity-image"></image>
			<view class="default-window">
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
		width: 100%;
		height: 300rpx;
		display: block;
		border-top-left-radius: 16rpx;
		border-top-right-radius: 16rpx;
	}
</style>
