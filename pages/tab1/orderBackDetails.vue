<template>
	<view>
		<uni-nav-bar color="#FFFFFF" left-icon="back" @clickLeft="onClickBack" class="header" status-bar="true" fixed="true"
		 v-if="headerShow" backgroundColor="rgba(0,0,0,0)" style="position: absolute; top: 0;">
		</uni-nav-bar>
		<uni-nav-bar color="#000000" left-icon="back" title="取单详情" @clickLeft="onClickBack" class="header" status-bar="true"
		 fixed="true" v-if="!headerShow" shadow="true" style="position: absolute; top: 0;">
		</uni-nav-bar>
		<!-- 内容 -->
		<view class="content" :class="{'content_active': order.detailStatus == 'waitpay' || order.detailStatus == 'cancel' || order.detailStatus == 'waitsign'}">
			<view class="cont_top">
				<view class="top_text">
					<view @longtap="longClick">
						<h4>{{order.detailStatusTitle}}</h4>
						<p>{{order.detailStatusSubTitle}}</p>
					</view>
				</view>
				<view class="top_button">
					<view class="button_left"></view>
					<view style="margin: 0 30upx;padding: 10upx 0;">
						<uni-list class="list_custom">
							<uni-list-item :showArrow="false">
								<view>
									<view class="top_button_left" v-if="order.detailStatus == 'finish'">
										<p>感谢您对存存的信任，期待再次光临</p>
										<text>you deserve better</text>
									</view>
									<view class="top_button_left" v-else>
										<p>{{order.orderTime}}</p>
										<text>申请时间</text>
									</view>
								</view>
								<view slot='right' class="top_button_right">
									<text @click="onCancelOrder(order.id)" v-if="order.detailStatus == 'waitpay'">取消订单</text>
									<image @click="onDetails" v-else src="../../static/tab2/more_info.png" mode=""></image>
								</view>
							</uni-list-item>
						</uni-list>
					</view>
				</view>
			</view>
			<view class="order_list">
				<p>返送清单</p>
				<view class="order_list_image">
					<image v-for="(item,index) in order.totalList" :key="index" :src="item.coverPic" mode="aspectFill"></image>
				</view>
				<view style="">
					<view class="flex_between order_list_fee">
						<p>第三方快递运输费</p>
						<text>¥ {{order.deliveryFee}}</text>
					</view>
					<view class="flex_between order_list_fee">
						<p>打包费用</p>
						<text>¥ {{order.packFee}}</text>
					</view>
					<view class="flex_between order_list_fee">
						<p>箱子费用</p>
						<text>¥ {{order.boxFee}}</text>
					</view>
					<view class="flex_between order_list_fee">
						<p>调整费用 <text style="color: #3BC1BB;margin-left: 20upx;" v-if="order.adjustFeeReason" @click="onChangeFeeAlert(order.adjustFeeReason)">查看</text></p>
						<text>¥ {{order.adjustFee}}</text>
					</view>
					<view class="flex_between order_list_fee">
						<p>需支付费用</p>
						<text>¥ <text style="font-size:32upx;margin-left: 10upx;">{{order.totalFee}}</text></text>
					</view>
				</view>
			</view>
			<view class="order_list">
				<p>返送信息</p>
				<view>
					<view class="row order_list_phone" style="align-items: flex-start;">
						<p class="col-4">上门地址</p>
						<view class="col-8" style="text-align: right;">
							<p style="color:rgba(40,40,40,1);">{{order.detailAddress}}</p>
							<text>{{order.linkman}} {{order.mobile}}</text>
						</view>
					</view>
					<view class="flex_between order_list_phone" v-if="order.waybillNo" @click="onCopy(order.waybillNo)">
						<p>运单号</p>
						<view>
							<text>{{order.waybillNo}}</text>
							<text style="margin: 0 30upx;color:rgba(222,222,222,1);">|</text>
							<text style="font-size:28upx;font-weight:400;color:rgba(2,105,208,1);">复制</text>
						</view>
					</view>
					<view class="flex_between order_list_phone" v-if="order.waybillNo" @click="onCopy(order.waybillNo)">
						<p>快递公司</p>
						<text>顺丰快递</text>
					</view>
				</view>
			</view>
			<view class="order_list">
				<p>订单信息</p>
				<view>
					<view class="flex_between order_list_phone" @click="onCopy(order.orderNo)">
						<p>订单号</p>
						<view>
							<text>{{order.orderNo}}</text>
							<text style="margin: 0 30upx;color:rgba(222,222,222,1);">|</text>
							<text style="font-size:28upx;font-weight:400;color:rgba(2,105,208,1);">复制</text>
						</view>
					</view>
					<view class="flex_between order_list_phone">
						<p>支付方式</p>
						<view>
							<text v-if="order.payChannel">{{order.payChannel}}支付</text>
						</view>
					</view>
					<view class="flex_between order_list_phone">
						<p>下单时间</p>
						<text>{{order.orderTime}}</text>
					</view>
					<view class="flex_between order_list_phone">
						<p>订单备注</p>
						<text>{{order.remark}}</text>
					</view>
				</view>
			</view>
			<view class="order_list">
				<p>客服信息</p>
				<view class="flex_between order_list_phone" @click="onCall">
					<p>客服电话</p>
					<view>
						<text>021-34283744</text>
						<text style="margin: 0 30upx;color:rgba(222,222,222,1);">|</text>
						<text style="font-size:28upx;font-weight:400;color:rgba(2,105,208,1);">拨打</text>
					</view>
				</view>
			</view>
		</view>
		<uni-popup ref="popupSteps" type="center">
			<view class="popup_steps_wrap">
				<view class="popup_title">
					<text>订单状态</text>
					<image class="close_btn" @click="onClosePopup" src="../../static/tab2/close.png" mode=""></image>
				</view>
				<scroll-view class="scroll_content" scroll-y="true">
					<custom-steps :options="optionsReverse" :active="active"></custom-steps>
				</scroll-view>
			</view>
		</uni-popup>
		<uni-popup ref="popupPay" type="bottom" @touchmove.stop.prevent @touchend.stop>
			<view class="popup_wrap">
				<view class="popup_title">
					<text>选择支付方式</text>
					<image class="close_btn" @click="closePayPopup" src="../../static/tab2/close.png" mode=""></image>
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
		<view class="flex_between bottom_pay" v-if="order.detailStatus == 'waitpay'">
			<text>¥ {{order.totalFee}}</text>
			<button @click="onPayChange" class="button_block" :class="{button_block_active: buttonActive}">确认支付</button>
		</view>
		<view class="flex_between bottom_pay" v-if="order.detailStatus == 'cancel'">
			<text></text>
			<button @click="onOrderAgain" class="button_block">重新下单</button>
		</view>
		<view class="flex_between bottom_pay" v-if="order.detailStatus == 'waitsign'">
			<text></text>
			<button @click="onConfirmGoods" class="button_block">确认收货</button>
		</view>
	</view>
</template>

<script>
	import customSteps from '../../components/customSteps.vue'
	export default {
		components: {
			'custom-steps': customSteps
		},
		data() {
			return {
				headerShow: true,
				order: '',
				orderId: '',
				gotoPage: '',
				options: [{
					title: '已完成',
					mmdd: '11-11',
					hhmm: '12:00',
					desc: '[取货地址]上海市 嘉定区 叶城五街坊 裕民1180弄78号102室'
				}, {
					title: '安检完成',
					mmdd: '11-11',
					hhmm: '12:00',
					desc: ''
				}, {
					title: '物品进入安检',
					mmdd: '11-11',
					hhmm: '12:00',
					desc: ''
				}, {
					title: '入库作业中',
					mmdd: '11-11',
					hhmm: '12:00',
					desc: ''
				}, {
					title: '回库中',
					mmdd: '11-11',
					hhmm: '12:00',
					desc: '物件正在运往存存库存中心物件正在运往存存库存中心物件正在运往存存库存中心物件正在运往存存库存中心物件正在运往存存库存中心物件正在运往存存库存中心'
				}, {
					title: '取货完成',
					mmdd: '11-11',
					hhmm: '12:00',
					desc: ''
				}, {
					title: '',
					mmdd: '11-11',
					hhmm: '12:00',
					desc: '[上海市]上海市骑手，正在前往取货，联系电话 23948029834'
				}, {
					title: '已受理',
					mmdd: '11-11',
					hhmm: '12:00',
					desc: ''
				}, {
					title: '等待接单',
					mmdd: '11-11',
					hhmm: '12:00',
					desc: ''
				}],
				active: 3,
				payStyleList: [{
						id: 0,
						value: 'Alipay',
						name: '支付宝',
						checked: true,
						imgUrl: '../../static/tab2/Alipay.png'
					},
					{
						id: 1,
						value: 'WeChatpay',
						name: '微信支付',
						checked: false,
						imgUrl: '../../static/tab2/WeChatpay.png'
					}
				],
				payStyle: 'Alipay',
				payStyleIndex: 0,
				buttonActive: false,
			}
		},
		onLoad(op) {
			console.log(op)
			if (op.id) {
				this.orderId = op.id
			}
			if (op.gotoPage) {
				this.gotoPage = op.gotoPage
			}
		},
		onShow() {
			if (this.orderId) {
				this.getOrderDetail()
				this.getOrderDetailSteps()
			}
		},
		computed: {
			optionsReverse() {
				let that = this
				return that.options.reverse()
			}
		},
		onPageScroll(options) {
			if (options.scrollTop > 60) {
				this.headerShow = false;
			} else {
				this.headerShow = true;
			}
		},
		onBackPress(e) {
			console.log(e)
			if (e.from == 'backbutton') {
				uni.setStorage({
					key: 'gotoPage',
					data: this.gotoPage,
					success: () => {
						uni.switchTab({
							url: '/pages/tabs/tab2',
						})
					}
				})
			}
		},
		watch: {},
		methods: {
			onClickBack() {
				if (this.gotoPage) {
					uni.setStorage({
						key: 'gotoPage',
						data: this.gotoPage,
						success: () => {
							uni.switchTab({
								url: '/pages/tabs/tab2',
							})
						}
					})
				} else {
					uni.navigateBack()
				}
			},
			onPayChange() {
				this.$refs.popupPay.open()
			},
			closePayPopup() {
				this.$refs.popupPay.close()
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
			// 支付
			onComfirmPay() {
				let orderObj = {
					orderId: this.orderId,
				}
				if (this.payStyle == 'Alipay') {
					this.$http('user/withdraw/order/pay/alipay', "POST", orderObj, res1 => {
						if (res1.data.success) {
							console.log(res1.data.data)
							this.$refs.popupPay.close()
							// #ifdef APP-PLUS
							uni.requestPayment({
								provider: 'alipay',
								orderInfo: res1.data.data,
								success: (res) => {
									this.order.payStyle = 'Alipay'
									uni.navigateTo({
										url: "/pages/tab1/orderBackSuccess?orderInfo=" + encodeURIComponent(JSON.stringify(this.order))
									})
								},
								fail: (err) => {
									this.$http('user/withdraw/order/pay/fail', "POST", orderObj, res2 => {
										this.getOrderDetail()
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
					this.$http('user/withdraw/order/pay/wechat', "POST", orderObj, res1 => {
						console.log(res1.data)
						if (res1.data.success) {
							console.log(res1.data.data)
							this.$refs.popupPay.close()
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
								success: (res) => {
									console.log(res)
									this.order.payStyle = 'WeChatpay'
									uni.navigateTo({
										url: "/pages/tab1/orderBackSuccess?orderInfo=" + encodeURIComponent(JSON.stringify(this.order))
									})
								},
								fail: (err) => {
									this.$http('user/withdraw/order/pay/fail', "POST", orderObj, res2 => {
										this.getOrderDetail()
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
			},
			// 订单详情
			getOrderDetail() {
				this.$http('user/withdraw/order/detail/' + this.orderId, "GET", '', res => {
					let data = res.data
					if (data.success) {
						data.data.detailAddress = `${data.data.area.province} ${data.data.area.city?data.data.area.city:''} ${data.data.area.district?data.data.area.district:''} ${data.data.plotName} ${data.data.address}`
						data.data.orderTime = this.$moment(data.data.timeCreated).format('YYYY-MM-DD HH:mm:ss')
						data.data.detailStatus = data.data.status.code
						data.data.detailStatusTitle = data.data.status.title
						data.data.detailStatusSubTitle = data.data.status.subTitle
						if (data.data.payChannel) {
							data.data.payChannel = data.data.payChannel.name //预付费支付方式
						}
						data.data.deliveryFee = data.data.deliveryFee.toFixed(2)
						data.data.packFee = data.data.packFee.toFixed(2)
						data.data.boxFee = data.data.boxFee.toFixed(2)
						data.data.adjustFee = data.data.adjustFee.toFixed(2)
						data.data.totalFee = data.data.totalFee.toFixed(2)
						data.data.totalList = []
						if (data.data.goods) {
							for (let item of data.data.goods) {
								item.code = item.goods.code
								item.coverPic = item.goods.coverPic
								item.name = item.goods.name
								data.data.totalList.push(item)
							}
						}
						if (data.data.packs) {
							for (let item of data.data.packs) {
								item.code = item.pack.code
								item.coverPic = '../../static/tab1/box_null.png'
								item.name = item.pack.name
								data.data.totalList.push(item)
							}
						}
						this.order = data.data
					} else {
						uni.showToast({
							icon: 'none',
							title: data.message
						});
					}
				})
			},
			// 订单状态
			getOrderDetailSteps() {
				this.$http('user/withdraw/workflow/list?orderId=' + this.orderId, "GET", '', res => {
					let data = res.data
					if (data.success) {
						if (data.data.length > 0) {
							for (let item of data.data) {
								item.title = item.status.name
								item.mmdd = this.$moment(item.timeCreated).format('MM-DD')
								item.hhmm = this.$moment(item.timeCreated).format('HH:mm:ss')
								item.desc = item.remark
							}
							this.options = data.data
							this.active = data.data.length - 1
						} else {
							this.options = []
							this.active = 0
						}
					} else {
						uni.showToast({
							icon: 'none',
							title: data.message
						});
					}
				})
			},
			// 订单取消
			onCancelOrder(id) {
				uni.showModal({
					title: '提示',
					content: '确认取消订单吗?',
					success: (res) => {
						if (res.confirm) {
							let data = {
								id: id
							}
							this.$http('user/withdraw/order/cancel', "POST", data, res => {
								let data = res.data
								if (data.success) {
									this.getOrderDetail()
								} else {
									uni.showToast({
										icon: 'none',
										title: data.message
									});
								}
							})
						}
					}
				})
			},
			// 确认收货
			onConfirmGoods() {
				uni.showModal({
					title: '提示',
					content: '是否确认收货?',
					success: (res) => {
						if (res.confirm) {
							let data = {
								id: this.orderId
							}
							this.$http('user/withdraw/order/confirm', "POST", data, res => {
								let data = res.data
								if (data.success) {
									this.getOrderDetail()
								} else {
									uni.showToast({
										icon: 'none',
										title: data.message
									});
								}
							})
						}
					}
				})
			},
			onOrderAgain() {
				let data = {
					id: this.orderId
				}
				this.$http('user/withdraw/order/recall', "POST", data, res => {
					let data = res.data
					if (data.success) {
						uni.navigateTo({
							url: '/pages/tab1/orderBack',
							success: () => {
								// #ifdef APP-PLUS
								uni.report('orderBackCopy', {
									'describe': '取单复制订单'
								})
								// #endif
							}
						})
					} else {
						uni.showToast({
							icon: 'none',
							title: data.message
						});
					}
				})

			},
			onDetails() {
				this.$refs.popupSteps.open()
			},
			onClosePopup() {
				this.$refs.popupSteps.close()
			},
			onChangeFeeAlert(index) {
				if (index) {
					uni.showModal({
						title: '提示',
						content: index,
						showCancel: false
					})
				}
			},
			onCopy(id) {
				// #ifdef APP-PLUS
				uni.setClipboardData({
					data: id,
					success: () => {
						console.log(id);
					}
				})
				// #endif
			},
			longClick() {
				if (this.order.detailStatus == 'cancel') {
					// #ifdef APP-PLUS
					uni.setClipboardData({
						data: 'yourvoicematters@shuncun.tech',
						success: () => {
							console.log(id);
						}
					})
					// #endif
				}
			},
			onCall() {
				uni.showModal({
					title: '提示',
					content: '是否要拨打客服电话021-34283744',
					success(res) {
						if (res.confirm) {
							// #ifdef APP-PLUS
							uni.makePhoneCall({
								phoneNumber: '021-34283744'
							});
							// #endif
						}
					}
				})
			}
		}
	}
</script>
<style>
	page {
		background: rgba(242, 242, 242, 1);
	}
</style>
<style scoped lang="scss">
	.content {
		width: 100%;
		height: 100%;
		box-sizing: border-box;
	}

	.content_active {
		padding: 0 0 130upx;
	}

	.cont_top {
		box-sizing: border-box;
		width: 100%;
		background: linear-gradient(rgba(6, 185, 185, 1) 0%, rgba(6, 185, 185, 1) 60%, rgba(59, 193, 187, 0) 100%);
		padding-top: 206upx;
		margin-bottom: 40upx;

		.top_text {
			font-size: 32upx;
			font-weight: 500;
			color: rgba(40, 40, 40, 1);
			line-height: 64upx;
			// text-align: justify;
			padding: 0 50upx;

			h4 {
				font-size: 46upx;
				font-weight: 600;
				color: rgba(255, 255, 255, 1);
				line-height: 56upx;
			}

			p {
				font-size: 30upx;
				font-weight: 400;
				color: rgba(255, 255, 255, 1);
				line-height: 52upx;
				margin-top: 10upx;
			}
		}

		.top_button {
			position: relative;
			width: 710upx;
			height: 148upx;
			background: rgba(255, 255, 255, 1);
			border-radius: 3upx;
			margin: 30upx auto 0;
			box-shadow: 0 2upx 4upx 2upx #f1f1f1;

			.button_left {
				position: absolute;
				top: 44upx;
				left: -10upx;
				z-index: 1;
				width: 26upx;
				height: 10upx;
				background: rgba(245, 166, 35, 1);
				border-radius: 5upx;
			}

			.top_button_left {
				p {
					font-size: 32upx;
					font-weight: 600;
					color: rgba(40, 40, 40, 1);
					line-height: 45upx;
				}

				text {
					font-size: 24upx;
					font-weight: 400;
					color: rgba(178, 178, 178, 1);
					line-height: 33upx;
					margin-top: 10upx;
				}
			}

			.top_button_right {
				text {
					display: block;
					width: 172upx;
					height: 60upx;
					border-radius: 5upx;
					text-align: center;
					border: 1upx solid rgba(221, 221, 221, 1);
					font-size: 28upx;
					line-height: 60upx;
					font-weight: 400;
					color: rgba(40, 40, 40, 1);
				}

				image {
					width: 36upx;
					height: 36upx;
				}
			}
		}
	}

	.order_list {
		margin: 20upx auto;
		width: 710upx;
		background: rgba(255, 255, 255, 1);
		border-radius: 3upx;
		padding: 30upx;
		box-sizing: border-box;

		p {
			font-size: 32upx;
			font-weight: 600;
			color: rgba(40, 40, 40, 1);
			line-height: 45upx;
			margin-top: 10upx;
		}

		.order_list_image {
			font-size: 0;
			padding: 40upx 0 20upx;
			border-bottom: 2upx solid rgba(242, 242, 242, 0.58);
			margin-bottom: 40upx;

			image {
				width: 140upx;
				height: 140upx;
				margin: 0 30upx 30upx 0;
			}

			image:nth-child(4n) {
				margin-right: 0;
			}
		}

		.order_list_fee {
			p {
				font-size: 24upx;
				font-weight: 400;
				color: rgba(178, 178, 178, 1);
				line-height: 33upx;
			}

			text {
				font-size: 20upx;
				font-weight: 600;
				color: rgba(40, 40, 40, 1);
				line-height: 28upx;
			}
		}

		.order_list_phone {
			margin-top: 40upx;
			padding: 40upx 0 10upx;
			border-top: 2upx solid rgba(242, 242, 242, 0.6);

			p {
				font-size: 28upx;
				font-weight: 400;
				color: rgba(178, 178, 178, 1);
			}

			text {
				font-size: 26upx;
				font-weight: 400;
				color: rgba(40, 40, 40, 1);
			}
		}
	}


	.input_remark {
		width: 420upx;
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

	.total_fee {
		text {
			font-size: 28upx;
			font-weight: 600;
			color: rgba(40, 40, 40, 1);
			line-height: 40upx;
		}
	}

	.popup_steps_wrap {
		position: relative;
		width: 707upx;
		height: 1000upx;
		background: rgba(255, 255, 255, 1);
		border-radius: 20upx;

		.scroll_content {
			height: 910upx;
		}
	}

	.popup_wrap {
		width: 100%;
		height: 660upx;
		background: rgba(255, 255, 255, 1);
		border-radius: 20upx 20upx 0 0;
		overflow-y: scroll;

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
			background: rgba(59, 193, 187, 1);
			border-radius: 3px;
			line-height: 80upx;
			font-size: 28upx;
			font-weight: 500;
			color: #FFFFFF;
			margin: 0;
		}
	}
</style>
