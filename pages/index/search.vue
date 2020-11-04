<template>
	<view>
		<u-sticky>
			<view class="white default-window search-window">
				<u-search :show-action="false" border-color="#fa3534" color="#333333" search-icon-color="#fa3534" bg-color="#FFFFFF"
				 placeholder="输入搜索内容" v-model="searchValue" @search="search"></u-search>
			</view>
		</u-sticky>
		<view class="default-window white">
			<u-subsection @change="change" active-color="#FFFFFF" buttonColor="#fa3534" :list="list" :current="current"></u-subsection>
		</view>

		<view v-if="current==0">
			<activity-list :loadlist="activityList"></activity-list>
			<view v-if="activityList.length==0" class="default-window">
				<u-empty text="活动搜索内容为空" mode="search"></u-empty>
			</view>
		</view>
		<view v-if="current==1">
			<article-list :loadlist="articleList"></article-list>
			<view v-if="articleList.length==0" class="default-window">
				<u-empty text="资讯搜索内容为空" mode="search"></u-empty>
			</view>
		</view>


	</view>
</template>

<script>
	export default {
		data() {
			return {
				searchValue: '',
				activityList: [],
				articleList: [],
				list: [{
					name: '活动'
				}, {
					name: '资讯'
				}],
				current: 0
			};
		},
		onLoad(data) {
			this.searchValue = data.searchValue;
			this.current = data.type;
			console.log(this.current)
			if (this.searchValue) {
				this.search();

			}
		},
		methods: {
			change(index) {
				this.current = index;
			},
			search() {
				this.search2();
				this.search1();
			},
			search1() {
				if (this.searchValue == '') {
					this.$showToast('请输入搜索内容')
				} else {
					let params = {
						name: this.searchValue,
						type: 1
					}
					this.$api('Activity/index', params).then(data => {
						if (data.status == 1) {
							this.activityList = data.data.activity_list;
						} else {
							this.$showToast(data.msg);
						}
					})

				}
			},
			search2() {
				if (this.searchValue == '') {
					this.$showToast('请输入搜索内容')
				} else {
					let params = {
						name: this.searchValue,
						type: 1
					}
					this.$api('Article/index', params).then(data => {
						if (data.status == 1) {
							this.articleList = data.data.article_list;
						} else {
							this.$showToast(data.msg);
						}
					})

				}
			},
		}
	}
</script>

<style lang="scss">

</style>
