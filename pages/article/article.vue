<template>
	<view>
		<u-sticky>
			<view class="white default-window search-window">
				<u-search :show-action="false" border-color="#fa3534" color="#333333" search-icon-color="#fa3534" bg-color="#FFFFFF"
				 placeholder="输入搜索内容" v-model="searchValue" @search="search"></u-search>
			</view>
		</u-sticky>
		<article-list :loadlist="articleList"></article-list>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				articleList: [],
				page: 1,
				searchValue:'',
			}
		},
		onReady() {
			this.loadArticle();
		},
		onReachBottom() {
			this.loadArticle();
		},
		methods: {
			search() {
				uni.navigateTo({
					url: '/pages/index/search?searchValue=' + this.searchValue + '&&type=1'
				})
			},
			//加载渔夫号资讯
			loadArticle() {
				this.page = this.page + 1;
				let params = {
					page: this.page,
					type: 1
				};
				this.$api('Article/index', params).then(data => {
					if (data.status == 1) {
						this.loadStatus = 'loading';
						let list = data.data.article_list;
						if (list.length < 10) {
							this.loadStatus = 'nomore';
						}
						for (let m in list) {
							this.articleList.push(list[m]);
							this.loadStatus = 'loadmore';
						}
					} else {
						this.$showToast(data.msg);
					}
				});
			}
		}
	}
</script>

<style>

</style>
