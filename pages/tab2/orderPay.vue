<template>
	<view>
		<uni-nav-bar color="#000000" left-icon="back" @clickLeft="onClickBack" class="header" status-bar="true" fixed="true"
		 v-if="headerShow" backgroundColor="rgba(0,0,0,0)">
		</uni-nav-bar>
		<uni-nav-bar color="#000000" left-icon="back" @clickLeft="onClickBack" class="header" status-bar="true" fixed="true"
		 v-if="!headerShow" shadow="true">
		</uni-nav-bar>
		<!-- 内容 -->
		<view class="content">
			<view class="cont_top">
				<image src="../../static/tab2/people.png"></image>
			</view>
			<view class="top_text">
				<text>我们经验丰富的收纳咨询师和训练有素的打包小哥正在准备，会按照跟您<text class="top_text_border">约定的时间到达</text>，请注意您的<text class="top_text_border">手机保持通知畅通</text>～</text>
			</view>
			<view style="margin-top: 60upx;">
				<uni-list class="list_custom list_custom_padding40">
					<uni-list-item @click="onChooseAddress">
						<view class="">
							<view class="" v-if="!address.id">
								<text>请添加您的地址</text>
							</view>
							<view class="choose_address" v-else>
								<view class="row address">
									<p class="address_detail">
										<uni-tag class="address_tag" :text="address.tag[0].name" size="small" :inverted="true" type="error"></uni-tag>
										<text>{{address.detailAddress}}</text>
									</p>
								</view>
								<view class="top_name">
									<text>{{address.linkman}}</text>
									<text style="margin-left: 30upx;">{{address.mobile}}</text>
								</view>
							</view>
						</view>
					</uni-list-item>
					<uni-list-item title="上门时间" @click="onDateChange">
						<view slot="right">
							<view style="font-size:28upx; color:rgba(3,166,166,1);">{{date}}</view>
						</view>
					</uni-list-item>
					<uni-list-item title="备注" :showArrow="false">
						<view slot="right">
							<input class="input_remark" v-model="userRemark" @blur="onOrderRemark" type="text" placeholder="对我们的服务人员有什么特别的吩咐吗"
							 style="font-size:28upx;padding-left: 20upx;color: #282828;" placeholder-style="font-size:14px; font-weight:400; color:rgba(178,178,178,1); line-height:40upx;" />
						</view>
					</uni-list-item>
				</uni-list>
			</view>
			<view class="pay_info">
				<view class="flex_between pay_fee">
					<text>支付定金</text>
					<text>¥ {{pay_fee}}</text>
				</view>
				<view class="flex_between pay_info_list">
					<text>运输费</text>
					<text>¥ {{pay_fee}}</text>
				</view>
				<view class="flex_between pay_info_list">
					<text>打包费</text>
					<text>¥ 0</text>
				</view>
				<view class="flex_between pay_info_list">
					<text>箱子费</text>
					<text>¥ 0</text>
				</view>
				<view style="font-size:26upx;font-weight:400;color:rgba(40,40,40,1);text-align: justify;line-height:42upx;margin: 60upx 0 20upx;">
					定金是根据您所需的箱子来暂定收费，实际费用以当天收到的物品为准进行多退少补，请放心。
					<br />存存承诺服务不出现任何隐形费用，服务人员也绝对不会出现追加费用现象，如有发现，欢迎告知。
				</view>
			</view>
		</view>
		<uni-popup ref="popupDate" type="bottom" @touchmove.stop.prevent @touchend.stop>
			<view class="popup_wrap">
				<view class="popup_title flex_between">
					<text class="popup_cancel" @click="closePopupDate">取消</text>
					<text>上门取件时间</text>
					<text class="popup_confirm" @click="confirmPopupDate">确认</text>
				</view>
				<view>
					<view class="row">
						<scroll-view style="width: 40%;height: 540upx;" scroll-y="true">
							<view class="date_item" :class="{'date_item_active': item.clicked}" @click="onChooseDates(item)" v-for="(item,index) in dates"
							 :key="index">{{item.value}}</view>
						</scroll-view>
						<scroll-view style="width: 60%;height: 540upx;" scroll-y="true" @click="onClickHours">
							<radio-group @change="onChooseHours">
								<label v-for="(item,index) in hours" :key="index">
									<view v-if="index >= hourDisableNum" class="flex_between date_item date_item_active">
										<view style="width: 78%;">
											{{item.value}}
										</view>
										<view style="width: 32%;">
											<radio :value="item.value" :checked="index === currentHour" :disabled="!dateDate || hourDisable" color="rgba(59, 193, 187, 1)" />
										</view>
									</view>
								</label>
								<view class="date_item date_item_active" v-if="hourDisableNum >= hours.length">
									<text>无可选时间</text>
								</view>
							</radio-group>
						</scroll-view>
					</view>
				</view>
			</view>
		</uni-popup>
		<uni-popup ref="popup" type="bottom" @touchmove.stop.prevent @touchend.stop>
			<view class="popup_wrap">
				<view class="popup_title">
					<text>选择支付方式</text>
					<image class="close_btn" @click="closePopup" src="../../static/tab2/close.png" mode=""></image>
				</view>
				<view class="popup_cont">
					<view class="">
						<radio-group @change="onPayChangeStyle">
							<uni-list class="list_custom list_custom_img56" v-for="(item, index) in payStyleList" :key="index">
								<label>
									<uni-list-item :title="item.name" :thumb="item.imgUrl" :showArrow="false">
										<view slot="right">
											<radio :value="item.value" :checked="payStyleIndex == index" color="rgba(59, 193, 187, 1)" />
										</view>
									</uni-list-item>
								</label>
							</uni-list>
						</radio-group>
					</view>
					<button class="pay_button" @click="onComfirmPay">立即支付</button>
				</view>
			</view>
		</uni-popup>
		<view class="flex_between bottom_pay">
			<text>¥ {{pay_fee}}</text>
			<button @click="onPayChange" :disabled="buttonDisable" class="button_block" :class="{button_block_active: buttonActive}">确认支付</button>
		</view>
	</view>
</template>

<script>
	export default {
		components: {},
		data() {
			return {
				indicatorStyle: `height: 90upx;`,
				headerShow: true,
				address: {
					id: ''
				},
				dates: [{
					id: 0,
					value: '07-27（周六）',
					checked: false
				}],
				hours: [{
					id: 0,
					value: '09:00～10:00',
					checked: 'false',
					valueStart: '09',
					valueEnd: '10'
				}],
				dateDate: '',
				hourValue: '',
				currentHour: -1,
				hourValue1: '09',
				hourValue2: '10',
				userRemark: '', //备注
				payStyleList: [{
						id: 0,
						value: 'Alipay',
						name: '支付宝',
						checked: 'false',
						imgUrl: '../../static/tab2/Alipay.png'
					},
					{
						id: 1,
						value: 'WeChatpay',
						name: '微信支付',
						imgUrl: '../../static/tab2/WeChatpay.png'
					}
				],
				payStyle: 'Alipay',
				payStyleIndex: 0,
				buttonActive: false,
				time: '12:01',
				date: '请选择上门时间',
				pay_fee: 0, // 支付费用
				boxNum: 0, // 箱子数量
				buttonDisable: false, //按钮禁止点击
				dateDisable: false, //日期禁止点击
				hourDisable: false, //日期禁止点击
				hourDisableNum: -1,
				nowDate: '', //当前日期
				nowHour: '' //当前时间
			}
		},
		onLoad(op) {
			this.boxNum = op.boxNum
		},
		onShow(e) {
			this.getDateList()
			this.getHoursList()
			this.address = uni.getStorageSync('address')
			this.pay_fee = 0
			if (!this.address.id) {
				this.getAddressList()
			}
			let orderRemark = uni.getStorageSync('orderRemark')
			if (orderRemark) {
				this.userRemark = orderRemark
			}

			this.nowDate = new Date().getDate()
			this.nowHour = new Date().getHours()
			if (this.nowDate < 10) {
				this.nowDate = '0' + this.nowDate
			}
			if (this.nowHour < 10) {
				this.nowHour = '0' + this.nowHour
			}
			
		},
		onPageScroll(options) {
			if (options.scrollTop > 60) {
				this.headerShow = false;
			} else {
				this.headerShow = true;
			}
		},
		watch: {
			address() {
				if (this.address.id && this.dateDate) {
					this.buttonActive = true
					this.buttonDisable = true
					this.getFee()
				} else {
					this.buttonActive = false
				}
			},
			dateDate() {
				if (this.address.id && this.dateDate) {
					this.buttonDisable = true
					this.buttonActive = true
				} else {
					this.buttonActive = false
				}
			}
		},
		methods: {
			onClickBack() {
				uni.navigateBack({
					delta: 1
				})
			},
			onChooseAddress() {
				uni.navigateTo({
					url: '../tab3/address?chooseAddress=true'
				})
			},
			onOrderRemark() {
				uni.setStorage({
					key: 'orderRemark',
					data: this.userRemark
				})
			},
			onDateChange() {
				this.$refs.popupDate.open()
			},
			closePopupDate() {
				this.$refs.popupDate.close()
			},
			confirmPopupDate() {
				this.$refs.popupDate.close()
				if (!this.dateDate || !this.hourValue) {
					this.date = '请选择上门时间'
					return
				}
				this.date = this.dateDate + this.hourValue
				this.getFee()
			},
			onChooseDates(item) {
				console.log(item)
				for (let dateItem of this.dates) {
					if (!item.isDisable) {
						if (item.date == dateItem.date) {
							dateItem.clicked = true
							this.dateDate = item.value
							if (this.nowDate == item.date.split('-')[2]) {
								if (this.nowHour >= 0 && this.nowHour <= 13) {
									this.hourDisableNum = 2 // 前两个不能选
								} else if (this.nowHour > 13 && this.nowHour <= 24) {
									this.hourDisableNum = this.hours.length
								}
								this.currentHour = -1
								this.hourValue = ''
							} else {
								this.hourDisable = false
								this.hourDisableNum = -1
							}
						} else {
							dateItem.clicked = false
						}
					} else {
						uni.showToast({
							title: '已约满, 请选择其他日期',
							icon: 'none'
						})
					}
				}
			},
			onChooseHours(evt) {
				console.log(evt.target.value)
				if (this.dateDate) {
					for (let i = 0; i < this.hours.length; i++) {
						if (this.hours[i].value === evt.target.value) {
							this.currentHour = i;
							this.hourValue1 = this.hours[i].valueStart
							this.hourValue2 = this.hours[i].valueEnd
							console.log(this.hourValue1)
							console.log(this.hourValue2)
							this.hourValue = this.hours[i].value
							break;
						}
					}
				}
			},
			onClickHours() {
				if (!this.dateDate) {
					uni.showToast({
						title: '请选择日期',
						icon: 'none'
					})
				}
				if(this.hourDisable) {
					uni.showToast({
						title: '当前时间不可选择',
						icon: 'none'
					})
				}
			},
			getFee() {
				if (!this.address.id) {
					uni.showToast({
						icon: 'none',
						title: '请选择地址'
					});
				} else if (!this.dateDate || !this.hourValue) {

				} else {
					this.pay_fee = '计算中...'
					let data = {
						addressId: this.address.id,
						h1: this.hourValue1,
						h2: this.hourValue2,
						boxNum: this.boxNum,
					}
					this.$http('user/deposit/param/freight', "POST", data, res => {
						let data = res.data
						this.buttonDisable = false
						if (data.success) {
							console.log(data)
							this.pay_fee = data.data
						} else {
							uni.showToast({
								icon: 'none',
								title: data.message
							});
						}
					})
				}
			},
			getAddressList() {
				this.$http('user/addr/list', "GET", '', res => {
					let data = res.data
					console.log(data)
					if (data.success) {
						if (data.data.length > 0) {
							let result = data.data.findIndex(value => {
								return value.dft == true;
							})
							for (let item of data.data) {
								item.detailAddress =
									`${item.area.province} ${item.area.city?item.area.city:''} ${item.area.district?item.area.district:''} ${item.plotName} ${item.address}`
							}
							if (uni.getStorageSync('address')) {
								let address = uni.getStorageSync('address')
								for (let item of data.data) {
									if (address.id == item.id) {
										uni.setStorage({
											key: 'address',
											data: item
										})
									}
								}
							} else {
								if (result == -1) {
									uni.setStorage({
										key: 'address',
										data: data.data[0],
									})
								} else {
									uni.setStorage({
										key: 'address',
										data: data.data[result],
									})
								}
							}
						} else {
							uni.removeStorage({
								key: 'address'
							})
						}
						this.address = uni.getStorageSync('address')
					} else {
						uni.showToast({
							icon: 'none',
							title: data.message
						});
					}
				})
			},
			onPayChange() {
				if (!this.address.id) {
					uni.showToast({
						title: '请选择地址',
						icon: 'none'
					})
				} else if (!this.dateDate) {
					uni.showToast({
						title: '上门时间',
						icon: 'none'
					})
				} else {
					this.$refs.popup.open()
					// #ifdef APP-PLUS
					uni.report('addOrderPayChange', {
						'describe': '存单进入支付'
					})
					// #endif
				}
			},
			closePopup() {
				this.$refs.popup.close()
			},
			getDateList() {
				this.$http('user/deposit/param/bookdate', "GET", '', res => {
					let data = res.data
					if (data.success) {
						for (let item of data.data) {
							if (Object.values(item)[0] == "约满") {
								item.isDisable = true
							} else {
								item.isDisable = false
							}
							item.clicked = false
							item.value = `${Object.keys(item)[0]} (${Object.values(item)[0]})`
							item.date = Object.keys(item)[0]
						}
						this.dates = data.data
					} else {
						uni.showToast({
							icon: 'none',
							title: data.message
						});
					}
				})
			},
			getHoursList() {
				this.$http('user/deposit/param/booktime', "GET", '', res => {
					let data = res.data
					if (data.success) {
						for (let item of data.data) {
							item.value = `${item[0]}:00 ~ ${item[1]}:00`
							item.valueStart = item[0]
							item.valueEnd = item[1]
							item.checked = ''
						}
						this.hours = data.data
					} else {
						uni.showToast({
							icon: 'none',
							title: data.message
						});
					}
				})
			},
			onPayChangeStyle(evt) {
				console.log(evt.target.value)
				this.payStyle = evt.target.value
				if (this.payStyle == 'Alipay') {
					this.payStyleIndex = 0
				} else {
					this.payStyleIndex = 1
				}
			},
			onComfirmPay() {
				let orderObj = {
					addressId: this.address.id,
					bookFetchDate: this.dateDate,
					bookFetchTime1: this.hourValue1,
					bookFetchTime2: this.hourValue2,
					prepaid: this.pay_fee,
					userRemark: this.userRemark
				}
				this.$http('user/deposit/order/create', "POST", orderObj, res => {
					let data = res.data
					console.log(data)
					if (data.success) {
						// 订单创建成功清除缓存物品
						uni.removeStorage({
							key: 'orderGoodsList'
						})
						uni.removeStorage({
							key: 'orderBoxsList'
						})
						uni.removeStorage({
							key: 'orderRemark'
						})
						let dataObj = {
							orderId: data.data.id
						}
						if (this.payStyle == 'Alipay') {
							this.$http('user/deposit/order/prepay/alipay', "POST", dataObj, res1 => {
								if (res1.data.success) {
									console.log(res1.data.data)
									this.$refs.popup.close()
									// #ifdef APP-PLUS
									uni.requestPayment({
										provider: 'alipay',
										orderInfo: res1.data.data,
										success: (respay) => {
											console.log(respay)
											uni.navigateTo({
												url: "/pages/tab2/orderSuccess?orderInfo=" + encodeURIComponent(JSON.stringify(data.data)),
												success: () => {
													// #ifdef APP-PLUS
													uni.report('addOrderAlipay', {
														'describe': '存单支付宝支付'
													})
													// #endif
												}
											})
										},
										fail: (err) => {
											console.log(err)
											this.$http('user/deposit/order/prepay/fail', "POST", dataObj, res2 => {
												uni.navigateTo({
													url: `/pages/tab2/orderDetails?id=${dataObj.orderId}&gotoPage=tab21`
												})
											})
										},
										complete: (e) => {}
									});
									// #endif
								} else {
									uni.showToast({
										icon: 'none',
										title: res1.data.message
									});
								}
							})
						} else {
							// 微信支付
							this.$http('user/deposit/order/prepay/wechat', "POST", dataObj, res1 => {
								if (res1.data.success) {
									console.log(res1.data.data)
									this.$refs.popup.close()
									let orderInfoObj = {
										"appid": res1.data.data.appid,
										"noncestr": res1.data.data.noncestr,
										"package": res1.data.data.package,
										"partnerid": res1.data.data.partnerid,
										"prepayid": res1.data.data.prepayid,
										"timestamp": res1.data.data.timestamp,
										"sign": res1.data.data.sign
									}
									// let orderInfo = JSON.stringify(orderInfoObj)
									// #ifdef APP-PLUS
									uni.requestPayment({
										provider: 'wxpay',
										orderInfo: orderInfoObj,
										success: (respay) => {
											console.log(respay)
											uni.navigateTo({
												url: "/pages/tab2/orderSuccess?orderInfo=" + encodeURIComponent(JSON.stringify(data.data)),
												success: () => {
													// #ifdef APP-PLUS
													uni.report('addOrderWxpay', {
														'describe': '存单微信支付'
													})
													// #endif
												}
											})
										},
										fail: (err) => {
											console.log(err)
											this.$http('user/deposit/order/prepay/fail', "POST", dataObj, res2 => {
												uni.navigateTo({
													url: `/pages/tab2/orderDetails?id=${dataObj.orderId}&gotoPage=tab21`
												})
											})
										},
										complete: (e) => {}
									});
									// #endif
								} else {
									uni.showToast({
										icon: 'none',
										title: res1.data.message
									});
								}
							})
						}
					} else {
						uni.showToast({
							icon: 'none',
							title: data.message
						});
					}
				})
			}
		}
	}
</script>

<style scoped lang="scss">
	.content {
		width: 100%;
		height: 100%;
		box-sizing: border-box;
		padding: 0 60upx 130upx;
	}

	.cont_top {
		text-align: center;

		image {
			width: 394upx;
			height: 260upx;
		}
	}

	.choose_address {
		.address {
			.address_detail {
				.address_tag {
					display: inline-block;
					height: 30upx;
					line-height: 30upx;
					font-size: 22upx;
					font-weight: 400;
					color: rgba(189, 103, 108, 1);
					margin-right: 30upx;
					vertical-align: middle;
				}

				text {
					font-size: 32upx;
					font-weight: 500;
					color: rgba(40, 40, 40, 1);
					line-height: 52upx;
					text-align: justify;
					vertical-align: middle;
				}
			}
		}

		.top_name {
			font-size: 26upx;
			font-weight: 400;
			color: rgba(178, 178, 178, 1);
			line-height: 37upx;
			margin-top: 10upx;
		}
	}

	.top_text {
		font-size: 32upx;
		font-weight: 500;
		color: rgba(40, 40, 40, 1);
		line-height: 64upx;
		text-align: justify;

		.top_text_border {
			box-sizing: border-box;
			border-bottom: 11upx solid #94DCD9;
		}
	}

	.input_remark {
		width: 500upx;
		height: 60upx;
		font-size: 28px;
		font-family: PingFangSC-Regular, PingFang SC;
		font-weight: 400;
		color: rgba(178, 178, 178, 1);
		line-height: 60upx;
		background: rgba(249, 249, 249, 0);
		border: 0 none;
		text-align: right;

		.item {
			width: 30%;
			height: 300px;
		}
	}

	.pay_info {
		margin: 40upx 0;

		.pay_info_list {
			font-size: 24upx;
			font-weight: 400;
			color: rgba(178, 178, 178, 1);
			line-height: 33upx;
			margin-top: 6upx;
		}
	}

	.pay_fee {
		text {
			font-size: 28upx;
			font-weight: 600;
			color: rgba(40, 40, 40, 1);
			line-height: 40upx;
		}
	}

	.popup_wrap {
		width: 100%;
		height: 660upx;
		background: rgba(255, 255, 255, 1);
		border-radius: 20upx 20upx 0 0;

		.popup_cancel {
			width: 140upx;
			height: 90upx;
			border-radius: 3px;
			line-height: 90upx;
			font-size: 28upx;
			font-weight: 500;
			color: #888888;
			text-align: center;
		}

		.popup_confirm {
			width: 140upx;
			height: 90upx;
			line-height: 90upx;
			font-size: 28upx;
			font-weight: 500;
			color: #FFFFFF;
			color: #3BC1BB;
		}
	}

	.popup_cont {
		box-sizing: border-box;
		padding: 30upx;

		.pay_button {
			width: 100%;
			height: 100upx;
			margin-top: 160upx;
			background: rgba(59, 193, 187, 1);
			border-radius: 3upx;
			font-size: 32upx;
			color: rgba(255, 255, 255, 1);
			line-height: 100upx;
		}
	}

	.date_item {
		width: 100%;
		height: 90upx;
		line-height: 90upx;
		text-align: center;
		font-size: 26upx;
		font-weight: 400;
		color: #B2B2B2;
		background-color: #F2F2F2;
		border-bottom: 1upx solid #F2F2F2;
	}

	.date_item_active {
		color: #282828;
		background-color: #FFFFFF;
	}

	.date_item:nth-last-child(1) {
		border-bottom: 0;
	}

	.bottom_pay {
		position: fixed;
		bottom: 0;
		width: 100%;
		box-sizing: border-box;
		height: 110upx;
		background: rgba(74, 74, 74, 1);
		box-shadow: 0 -2upx 10upx 0 rgba(0, 0, 0, 0.05);
		padding: 0 30upx;

		text {
			font-size: 36upx;
			font-weight: 600;
			color: rgba(255, 255, 255, 1);
		}

		.button_block {
			width: 212upx;
			height: 80upx;
			background-color: #B2B2B2;
			border-radius: 3px;
			line-height: 80upx;
			font-size: 28upx;
			font-weight: 500;
			color: #FFFFFF;
			margin: 0;
		}

		.button_block_active {
			background: rgba(59, 193, 187, 1);
		}
	}
</style>
