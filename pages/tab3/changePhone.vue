<template>
	<view class="layout">
		<uni-nav-bar left-icon="back" @clickLeft="onClickBack" title="修改手机号" status-bar="true" fixed="true"></uni-nav-bar>
		<!-- 内容 -->
		<view style="margin-top: 20upx;padding: 0 30upx;background-color: #FFFFFF;">
			<uni-list class="list_custom list_custom_item list_custom_margin20">
				<uni-list-item title="当前手机：" :showArrow="false">
					<view slot="right" class="input">
						<input type="text" v-model="userOld" disabled maxlength="11" placeholder="请输入旧的手机号码" placeholder-style="color: #CCCCCC;font-size:14px;" />
					</view>
				</uni-list-item>
				<uni-list-item title="验证码：" :showArrow="false">
					<view slot="right" class="sms row">
						<input type="number" v-model="smsOld" maxlength="6" class="col-8" placeholder="请输入旧手机验证码" placeholder-style="color: #CCCCCC;font-size:14px;" />
						<button :disabled="disabledOld" @click="getSmsOld" class="sms_button col-4">{{smsTextOld}}</button>
					</view>
				</uni-list-item>
				<uni-list-item title="新手机：" :showArrow="false">
					<view slot="right" class="input">
						<input type="number" v-model="userNew" maxlength="11" placeholder="请输入新的手机号码" placeholder-style="color: #CCCCCC;font-size:14px;" />
					</view>
				</uni-list-item>
				<uni-list-item title="验证码：" :showArrow="false">
					<view slot="right" class="sms flex_between">
						<input type="number" v-model="smsNew" maxlength="6" class="col-8" placeholder="请输入新手机验证码" placeholder-style="color: #CCCCCC;font-size:14px;" />
						<button :disabled="disabledNew" @click="getSmsNew" class="sms_button col-4">{{smsTextNew}}</button>
					</view>
				</uni-list-item>
			</uni-list>
		</view>
		<button type="default" @click="onConfirm" class="address_button" :class="{address_button_active: buttonActive}">确定</button>
	</view>
</template>

<script>
	export default {
		components: {},
		data() {
			return {
				userOld: "", //手机号
				smsOld: "", //验证码
				userNew: "", //手机号
				smsNew: "", //验证码
				smsTextOld: "发送验证码",
				smsTextNew: "发送验证码",
				buttonActive: false, // 颜色控制
				disabledOld: false, // 是否可以点击
				disabledNew: false, // 是否可以点击
			};
		},
		onLoad() {
			
		},
		onShow() {
			let user = uni.getStorageSync('user')
			console.log(user)
			if (user.mobile) {
				this.userOld = user.mobile
			}
		},
		watch: {
			userOld() {
				if (this.userOld && this.smsOld && this.userNew && this.smsNew) {
					this.buttonActive = true;
				} else {
					this.buttonActive = false;
				}
			},
			smsOld() {
				if (this.userOld && this.smsOld && this.userNew && this.smsNew) {
					this.buttonActive = true;
				} else {
					this.buttonActive = false;
				}
			},
			userNew() {
				if (this.userOld && this.smsOld && this.userNew && this.smsNew) {
					this.buttonActive = true;
				} else {
					this.buttonActive = false;
				}
			},
			smsNew() {
				if (this.userOld && this.smsOld && this.userNew && this.smsNew) {
					this.buttonActive = true;
				} else {
					this.buttonActive = false;
				}
			}
		},
		methods: {
			onClickBack() {
				uni.navigateBack({
					delta: 1
				})
			},
			getSmsOld() {
				if (!this.userOld || this.userOld.length != 11) {
					uni.showToast({
						title: '请输入正确的手机号',
						icon: 'none'
					})
				} else {
					uni.request({
					    url: 'http://cuncun.app.iisu.cn/server/sso/code/send',
					    data: {
							type: 'mobile',
							account: this.userOld
						},
						method: 'POST',
					    header: {
							'Content-Type': 'application/x-www-form-urlencoded',
					        'X-TENANT-ID': 'cuncun:cc@2020'
					    },
					    success: (res) => {
					        let data = res.data
							if (data.success) {
								uni.showToast({
									icon: 'none',
									title: data.message
								});
								this.disabledOld = true;
								let num = 30;
								this.smsTextOld = num;
								this.timerOld = setInterval(() => {
									num--;
									if (num >= 0) {
										this.smsTextOld = num;
									} else {
										clearInterval(this.timerOld);
										this.smsTextOld = "再次获取";
										this.disabledOld = false;
									}
								}, 1000);
							} else {
								uni.showToast({
									icon: 'none',
									title: data.message
								});
							}
					    }
					});
				}
			},
			getSmsNew() {
				if (!this.userNew || this.userNew.length != 11) {
					uni.showToast({
						title: '请输入正确的手机号',
						icon: 'none'
					})
				} else {
					uni.request({
					    url: 'http://cuncun.app.iisu.cn/server/sso/code/send',
					    data: {
							type: 'mobile',
							account: this.userNew
						},
						method: 'POST',
					    header: {
							'Content-Type': 'application/x-www-form-urlencoded',
					        'X-TENANT-ID': 'cuncun:cc@2020'
					    },
					    success: (res) => {
					        let data = res.data
							if (data.success) {
								uni.showToast({
									icon: 'none',
									title: data.message
								});
								this.disabledNew = true;
								let num = 30;
								this.smsTextNew = num;
								this.timerNew = setInterval(() => {
									num--;
									if (num >= 0) {
										this.smsTextNew = num;
									} else {
										clearInterval(this.timerNew);
										this.smsTextNew = "再次获取";
										this.disabledNew = false;
									}
								}, 1000);
							} else {
								uni.showToast({
									icon: 'none',
									title: data.message
								});
							}
					    }
					});
				}
			},
			onConfirm() {
				if (!this.userOld) {
					uni.showToast({
						title: '请输入旧的手机号码',
						icon: 'none'
					})
				} else if (!this.smsOld) {
					uni.showToast({
						title: '请输入旧手机验证码',
						icon: 'none'
					})
				} else if (!this.userNew) {
					uni.showToast({
						title: '请输入新的手机号码',
						icon: 'none'
					})
				} else if (!this.smsNew) {
					uni.showToast({
						title: '请输入新手机验证码',
						icon: 'none'
					})
				} else {
					uni.request({
					    url: 'http://cuncun.app.iisu.cn/server/sso/self/reset/mobile',
					    data: {
							oldCode: this.smsOld,
							newMobile: this.userNew,
							newCode: this.smsNew
						},
						method: 'POST',
					    header: {
							'Content-Type': 'application/x-www-form-urlencoded',
					        'X-TENANT-ID': 'cuncun:cc@2020',
							'Authorization': uni.getStorageSync('token')
					    },
					    success: (res) => {
					        let data = res.data
							if (data.success) {
								uni.showToast({
									icon: 'none',
									title: '修改成功'
								});
								let pages = getCurrentPages(); //获取所有页面栈实例列表
								let nowPage = pages[pages.length - 1]; //当前页页面实例
								let prevPage = pages[pages.length - 2]; //上一页页面实例
								prevPage.$vm.userInfoUpdate = true; //修改上一页data里面的couponNumber参数值为value
								uni.navigateBack();
							} else {
								uni.showToast({
									icon: 'none',
									title: data.message
								});
							}
					    }
					});
				}
			}
		}
	};
</script>

<style>
	page {
		background-color: #F9F9F9;
	}
</style>

<style scoped lang="scss">
	.header_icon {
		width: 200upx;
		height: 44px;
	}

	.input {
		width: 520upx;
		font-weight: 400;
		font-size: 28upx;
		line-height: 40upx;
	}
	.sms {
		width: 520upx;
		input {
			width: 370upx;
		}
		.sms_button {
			display: block;
			width: 120upx;
			font-size: 28upx;
			font-weight: 400;
			color: rgba(3, 166, 166, 1);
			line-height: 80upx;
			background-color: rgba(0, 0, 0, 0);
			text-align: right;
			padding: 0;
		}
		
		.sms_button:after {
			border: 0 none;
		}
	}

	.address_button {
		width: 690upx;
		height: 98upx;
		background: rgba(238, 238, 238, 1);
		border-radius: 6upx;
		font-size: 30upx;
		font-weight: 500;
		color: rgba(178, 178, 178, 1);
		line-height: 98upx;
		text-align: center;
		margin-top: 80upx;
	}

	.address_button_active {
		background-color: #3BC1BB;
		color: #FFFFFF;
	}
</style>
