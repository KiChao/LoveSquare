<template>
	<view>
		<view class="card default-window">
			<view style="border-left: 10upx solid #FA3534;padding: 4upx 20upx;">
				<view v-if="current<=question_num">第{{current}}/{{question_num}}题</view>
				<view v-else>答题完成</view>
				<text v-if="current<=question_num">{{question.name}}</text>
			</view>

			<view class="default-window">
				<radio-group @change="radioChange">
					<label class="uni-list-cell-pd" v-for="(item, index) in answers" :key="index">
						<view class="flex" style="margin-bottom: 20rpx;">
							<view style="width: 60upx;">
								<radio :checked="answer==index" :value="index" />
							</view>
							<view>
								{{item}}
							</view>
						</view>
					</label>
				</radio-group>
			</view>
		</view>
		<view class="default-window">
			<u-button :disabled="btnDisabled" @click="goAnswer" v-if="current<=question_num" type="error" shape="circle">下一题
			</u-button>
			<u-button @click="finish" v-else type="error" shape="circle">答题完成</u-button>
		</view>
		<view @click="goBack()" class="default-window u-text-center">
			返回上一题
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				loadId: '',
				answers: [],
				question: {},
				answer: '',
				next: '',
				btnDisabled: false,
				question_num: 0,
				current:0,
			};
		},
		onLoad(data) {
			this.loadId = data.loadId;
		},
		onReady() {
			this.loadQuestion();
		},
		methods: {
			loadQuestion() {
				this.$api('RebTest/get_question', {
					reb_test_id: this.loadId
				}).then(data => {
					if (data.status == 1) {
						if (data.data.question) {
							this.answers = data.data.question.answers;
						}
						this.current=data.data.current;
						this.question = data.data.question;
						this.question_num = data.data.question_num;
						this.next = data.data.next;
						this.btnDisabled = false;
						this.answer = '';
					} else {
						this.$showToast(data.msg);
					}
				})
			},
			radioChange(e) {
				this.answer = e.detail.value;
			},
			goAnswer() {
				if (this.answer == '') {
					this.$showToast('请选择答案');
					return;
				}
				let params = {
					reb_question_id: this.question.reb_question_id,
					answer: this.answer,
					reb_test_id: this.loadId
				}
				this.btnDisabled = true;
				this.$api('RebTest/answer', params).then(data => {

					if (data.status == 1) {
						if(this.current==this.question_num){
							this.$showToast('答题完成');
							setTimeout(()=>{
								uni.navigateBack({
								
								})
							},1000)
						}else{
							this.loadQuestion();
						}
						

					} else {
						this.$showToast(data.msg);
						this.btnDisabled = false;
					}
				})
			},
			//返回上一题
			goBack() {
				this.$api('RebTest/go_back', {
					reb_test_id: this.loadId
				}).then(data => {
					if (data.status == 1) {
						this.loadQuestion();
					} else {
						this.$showToast(data.msg);
					}
				})
			},
			//结束答题
			finish() {
				uni.navigateBack({

				})
			}
		}
	}
</script>

<style lang="scss">

</style>
