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
				<image src="../../static/tab2/save_top.png" mode=""></image>
			</view>
			<view class="top_text">
				<text>我们将为你的物品提供高标准的日式管理，维护，让你无顾之忧的同时，又一手轻松掌握。请简单告知我们你要存的东西，以便我们准备打包材料。</text>
			</view>
			<view class="add_content">
				<view v-for="(item,index) in inputList" :key="index" style="margin-top: 40upx;">
					<view class="flex_between" v-if="item.number != 0">
						<input class="add_input" type="text" @blur="inputText" :focus="inputFocus" @confirm="inputConfirm" v-model="item.value"
						 placeholder="如：文档，书本…" confirm-type="go" style="font-size:28upx;padding-left: 20upx;color: #282828;"
						 placeholder-style="font-size:14px; font-weight:400; color:rgba(178,178,178,1); line-height:40upx;" />
						<uni-number-box class="number_box_custom" :disabledInput="true" :min="0" :max="9999" :value="item.number" @change="changeInputNumber($event, index, item)" />
					</view>
				</view>
				<view class="flex_between" style="margin-top: 60upx;">
					<text class="button button_left" @click="onAddList">+ 添加</text>
					<text class="button button_right" @click="$refs.popupAlert.open()">储存注意事项 ></text>
				</view>
			</view>
			<view style="margin-top: 40upx;">
				<view class="box_size">
					<h4>请选择储存纸箱：</h4>
					<text>请根据您的需求选择您所需要用到的箱子</text>
					<view class="flex_between" v-for="(item,index) in boxList" v-show="index <= boxNumber" :key="index" style="margin-top: 40upx;">
						<view class="collapse_left">
							<h4>{{item.name}}</h4>
							<p>重量上限(kg)：{{item.weight}}</p>
							<p>储存费用/月(¥)：{{item.storePerMonthFee}}</p>
							<text @click="onBoxDetail(item.id)">纸箱详情介绍 ></text>
						</view>
						<uni-number-box class="number_box_custom" :disabledInput="true" :min="0" :max="9999" :value="item.number" @change="changeBoxNumber($event,index, item)" />
					</view>
					<view @click="onSeeMore" class="button_more">
						<text>{{boxButtonText}}</text>
						<image :class="{image_active: boxNumber == 9999}" src="../../static/tab1/arrows.png" mode=""></image>
					</view>
				</view>
				<view class="border_fee">
					<uni-list class="list_custom">
						<uni-list-item title="预计存储费用：" note="根据您所选的箱子预计每月存储费用" :showArrow="false">
							<view slot='right' class="total_fee">
								<p>¥<text>{{total_fee}}</text></p>
							</view>
						</uni-list-item>
					</uni-list>
				</view>
				<view class="row total_tip">
					<view class="total_tip_right">
						<p>只要花不到一顿饭的钱，你会惊讶的发现：你的房子更大了，不用担心找不到东西了。</p>
					</view>
				</view>
			</view>
			<label class="row flex_between" style="margin-top: 60upx;">
				<view class="col-1" style="margin: -30upx 0 0 -12upx;">
					<checkbox-group @change="agreement(2)">
						<checkbox style="transform:scale(0.6);" color="rgba(59, 193, 187, 1)" />
					</checkbox-group>
				</view>
				<view class="row col-11">
					<p style="font-size: 26upx;color: #282828;margin-top: -30upx;text-align: justify;">
						<text>我已经认真阅读并同意</text>
						<text @click="onAgreement" style="color: #0269D0;">《用户服务协议》</text>
						<text>，对于我要存储的东西都符合协议规定</text>
					</p>
				</view>
			</label>
		</view>
		<uni-popup ref="popupAlert" :maskClick='false' type="center">
			<view class="popup_alert_wrap">
				<view class="popup_title">
					<text>存单须知</text>
					<image class="close_btn" @click="onClosePopup" src="../../static/tab2/close.png" mode=""></image>
				</view>
				<scroll-view class="scroll_content" scroll-y="true">
					<alert-agreement></alert-agreement>
				</scroll-view>
				<view class="alert_bottom">
					<label class="row">
						<view class="col-5" style="text-align: right;">
							<checkbox-group @change="agreement(3)">
								<checkbox style="transform:scale(0.6);" :checked="agree3" color="rgba(59, 193, 187, 1)" />
							</checkbox-group>
						</view>
						<view class="row col-7" style="font-size: 26upx;color: #282828; margin-top: 4upx;">
							<text>不再显示</text>
						</view>
					</label>
					<button @click="onClosePopup" class="alert_button">我知道了</button>
				</view>
			</view>
		</uni-popup>
		<uni-popup ref="popup" type="bottom" @touchmove.stop.prevent @touchend.stop>
			<view class="popup_wrap">
				<view class="popup_title">
					<text>纸箱详情介绍</text>
					<image class="close_btn" @click="closePopup" src="../../static/tab2/close.png" mode=""></image>
				</view>
				<scroll-view style="height: 900upx;" scroll-y="true">
					<view class="popup_cont" v-for="(item,index) in boxList" :key="index">
						<view class="popup_item" v-if="boxIndex == item.id">
							<view class="flex_between">
								<view>
									<image style="width: 310upx;height: 310upx;" :src="item.pic"></image>
								</view>
								<view class="collapse_left">
									<h4>{{item.name}}</h4>
									<p>箱子规格(cm)：{{item.length}}*{{item.width}}*{{item.height}}</p>
									<p>重量(kg)：{{item.weight}}</p>
									<p>储存费用/月(¥)：{{item.storePerMonthFee}}</p>
								</view>
							</view>
							<text style="font-size:26upx;color:rgba(40,40,40,1);line-height:37upx;" v-if="item.remark">{{item.box.remark}}</text>
							<image style="width:688upx;height:440upx;margin-top: 40upx;" v-if="item.sceneryPic" :src="item.box.sceneryPic"></image>
						</view>
					</view>
				</scroll-view>
			</view>
		</uni-popup>
		<button @click="onNext" class="button_block" :disabled="buttonDisable" :class="{button_block_active: buttonActive}">下一步</button>
	</view>
</template>

<script>
	import alertAgreement from '../../components/alertAgreement.vue'
	export default {
		components: {
			'alert-agreement': alertAgreement
		},
		data() {
			return {
				info: Object,
				headerShow: true,
				inputList: [{
					id: 0,
					value: "",
					number: '1'
				}],
				inputListNumber: 0,
				boxList: [{
					id: 0,
					name: "小型纸箱A（拍照）",
					weight: 120,
					fee: 18,
					number: 0,
					storePerMonthFee: 0
				}],
				boxIndex: '',
				mday: 0, //每月有多少天
				boxNumber: 1,
				boxButtonText: '显示全部纸箱',
				agree1: false,
				agree2: false,
				agree3: false,
				buttonActive: false,
				buttonDisable: false,
				orderCopy: false, //订单复制
				orderGoodsList: [],
				orderBoxsList: [],
				inputFocus: false
			}
		},
		onLoad(option) {
			if (option.orderCopy == 'true') {
				console.log(option.orderCopy)
				this.orderCopy = true
				this.getUserInputList()
			} else {
				this.orderGoodsList = uni.getStorageSync('orderGoodsList')
				this.orderBoxsList = uni.getStorageSync('orderBoxsList')
				if (!this.orderGoodsList) {
					uni.setStorage({
						key: 'orderGoodsList',
						data: this.orderGoodsList
					})
				} else {
					for (let i = 0; i < this.orderGoodsList.length; i++) {
						if (this.orderGoodsList[i].number == 0 || this.orderGoodsList[i].value == '') {
							this.orderGoodsList.splice(i, 1)
						}
					}
					if (this.orderGoodsList.length > 0) {
						this.inputList = this.orderGoodsList
					}
				}
				if (!this.orderBoxsList) {
					uni.setStorage({
						key: 'orderBoxsList',
						data: this.orderBoxsList
					})
				}
			}
			this.getBoxList()
			let myDate = new Date()
			let yy = myDate.getFullYear() //获取完整的年份(4位,1970-????)
			let mm = myDate.getMonth() + 1 //获取当前月份(0-11,0代表1月)
			if ((yy % 4 == 0 && yy % 100 != 0) || yy % 400 == 0) {
				if (mm == 2) {
					this.mday = 29
				} else if (mm == 4 || mm == 6 || mm == 9 || mm == 11) {
					this.mday = 30
				} else {
					this.mday = 31
				}
			} else {
				if (mm == 2) {
					this.mday = 28
				} else if (mm == 4 || mm == 6 || mm == 9 || mm == 11) {
					this.mday = 30
				} else {
					this.mday = 31
				}
			}
			// #ifdef APP-PLUS
			uni.report('addOrderEdit', {
				'describe': '添加存单页'
			})
			// #endif

		},
		onShow() {
			this.$nextTick(() => {
				let alertAgreement = uni.getStorageSync('alertAgreement')
				if (!alertAgreement) {
					this.$refs.popupAlert.open()
				} else {
					this.agree3 = true
				}
			})
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
			console.log(666666666)
			// #ifdef APP-PLUS
			uni.hideKeyboard()
			// #endif
		},
		watch: {
			agree2() {
				if (this.agree2) {
					this.buttonActive = true;
				} else {
					this.buttonActive = false;
				}
			}
		},
		computed: {
			total_fee() {
				let fee = 0
				for (let item of this.boxList) {
					fee += Number(item.storePerMonthFee) * Number(item.number)
				}
				return fee.toFixed(2)
			}
		},
		methods: {
			onClickBack() {
				uni.navigateBack({
					delta: 1
				})
			},
			onSeeMore() {
				if (this.boxNumber == 1) {
					this.boxNumber = 9999
					this.boxButtonText = '收起全部纸箱'
				} else {
					this.boxNumber = 1
					this.boxButtonText = '显示全部纸箱'
				}
			},
			onAddList() {
				this.inputListNumber++
				this.inputList.push({
					id: this.inputListNumber,
					value: '',
					number: '1'
				})
			},
			inputText() {
				uni.setStorage({
					key: 'orderGoodsList',
					data: this.inputList
				})
			},
			inputConfirm() {
				this.inputFocus = false
				// #ifdef APP-PLUS
				uni.hideKeyboard(); //隐藏软键盘
				plus.key.hideSoftKeybord();
				// #endif
			},
			// 物品加减
			changeInputNumber(number, index, item) {
				if (number <= 0) {
					uni.showModal({
						title: '提示',
						content: '是否删除该物品？',
						success: (res) => {
							if (res.confirm) {
								if (typeof(item.id) == 'string') {
									let data = {
										id: item.id
									}
									this.$http('user/deposit/goods/del', "POST", data, res => {
										let data = res.data
										if (data.success) {
											this.inputList[index].number = 0
											// this.inputList.splice(index, 1)
											uni.setStorage({
												key: 'orderGoodsList',
												data: this.inputList
											})
											uni.showToast({
												icon: 'none',
												title: '删除成功'
											});
										} else {
											uni.showToast({
												icon: 'none',
												title: data.message
											});
										}
									})
								} else {
									// let result = this.inputList.findIndex(value => {
									// 	return value.id == index;
									// });
									this.$nextTick(() => {
										setTimeout(() => {
											this.inputList[index].number = 0
											// this.inputList.splice(index, 1)
											uni.setStorage({
												key: 'orderGoodsList',
												data: this.inputList
											})
											uni.showToast({
												icon: 'none',
												title: '删除成功'
											});
										}, 50)
									})
								}
							} else if (res.cancel) {
								this.inputList[index].number = '1'
							}
						}
					})
				} else {
					this.inputList[index].number = number
					uni.setStorage({
						key: 'orderGoodsList',
						data: this.inputList
					})
				}
			},
			// 箱子增减
			changeBoxNumber(number, index, item) {
				if (number <= 0) {
					let data = {
						id: item.box.id
					}
					this.$http('user/deposit/box/del', "POST", data, res => {
						let data = res.data
						this.boxList[index].number = number;
						uni.setStorage({
							key: 'orderBoxsList',
							data: this.boxList
						})
					})
				} else {
					this.boxList[index].number = number;
					uni.setStorage({
						key: 'orderBoxsList',
						data: this.boxList
					})
				}

			},
			// 注意事项
			onClosePopup() {
				let alertAgreement = uni.getStorageSync('alertAgreement')
				if (this.agree3) {
					alertAgreement = true // 不显示
				} else {
					alertAgreement = false // 显示
				}
				uni.setStorage({
					key: 'alertAgreement',
					data: alertAgreement,
					success: () => {
						this.$refs.popupAlert.close()
					}
				})
				// #ifdef APP-PLUS
				uni.report('addOrderAlert', {
					'describe': '添加存单弹窗'
				})
				// #endif
			},
			// 箱子详情
			onBoxDetail(id) {
				this.boxIndex = id
				this.$refs.popup.open()
				// #ifdef APP-PLUS
				uni.report('addOrderBoxDetail', {
					'describe': '存单箱子弹窗'
				})
				// #endif
			},
			closePopup() {
				this.$refs.popup.close()
			},
			agreement(index) {
				if (index == 2) {
					this.agree2 = !this.agree2
				} else if (index == 3) {
					this.agree3 = !this.agree3
				}
			},
			onNext() {
				let dataInput = {}
				let dataBox = {}
				let inputIndex = 0
				let boxIndex = 0
				let boxTotalNum = 0
				for (let item of this.inputList) {
					if (item.number > 0 && item.value) {
						dataInput['name[' + inputIndex + ']'] = item.value
						dataInput['amount[' + inputIndex + ']'] = item.number
						inputIndex++
					}
				}
				for (let item of this.boxList) {
					if (item.number > 0) {
						dataBox['boxId[' + boxIndex + ']'] = item.box.id
						dataBox['amount[' + boxIndex + ']'] = item.number
						boxIndex++
						boxTotalNum += Number(item.number)
					}
				}
				console.log(boxTotalNum)
				if (!inputIndex || !boxIndex) {
					if (!inputIndex) {
						uni.showToast({
							title: '请添加物品',
							icon: 'none'
						})
					} else if (!boxIndex) {
						uni.showToast({
							title: '请选择箱子',
							icon: 'none'
						})
					}
				} else if (!this.agree2) {
					uni.showToast({
						title: '您必须同意协议',
						icon: 'none'
					})
				} else {
					this.buttonDisable = true
					this.$http('user/deposit/goods/add', "POST", dataInput, res => {
						let data = res.data
						if (data.success) {
							this.$http('user/deposit/box/add', "POST", dataBox, res => {
								let data = res.data
								if (data.success) {
									uni.navigateTo({
										url: "/pages/tab2/orderPay?boxNum=" + boxTotalNum,
										success: () => {
											this.buttonDisable = false
											// #ifdef APP-PLUS
											uni.report('addOrderNext', {
												'describe': '存单下一步'
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
						} else {
							uni.showToast({
								icon: 'none',
								title: data.message
							});
						}
					})
				}
			},
			getBoxList() {
				this.$http('user/deposit/box/list', "GET", '', res => {
					let data = res.data
					if (data.success) {
						for (let item of data.data) {
							item.number = item.amount
							item.id = item.box.id
							item.name = item.box.name
							item.length = item.box.length
							item.width = item.box.width
							item.height = item.box.height
							item.weight = item.box.weight
							item.sceneryPic = item.box.sceneryPic
							item.pic = item.box.pic
							item.storePerMonthFee = item.box.storePerMonthFee.toFixed(2)
							item.remark = item.box.remark
						}
						this.boxList = data.data
						if (this.orderCopy) {

						} else {
							this.orderBoxsList = uni.getStorageSync('orderBoxsList')
							for (let boxItem of this.boxList) {
								for (let item of this.orderBoxsList) {
									if (boxItem.box.id == item.id) {
										boxItem.number = item.number
									}
								}
							}
						}
					} else {
						uni.showToast({
							icon: 'none',
							title: data.message
						});
					}
				})
			},
			getUserInputList() {
				this.$http('user/deposit/goods/list', "GET", '', res => {
					let data = res.data
					if (data.success) {
						for (let item of data.data) {
							item.value = item.name
							item.number = item.amount
						}
						this.inputList = data.data
					}
				})
			},
			onAgreement() {
				uni.navigateTo({
					url: '/pages/login/agreement'
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
		padding: 0 60upx 120upx;

		.cont_top {
			text-align: center;

			image {
				width: 262upx;
				height: 189upx;
			}
		}

		.top_text {
			font-size: 32upx;
			font-weight: 500;
			color: rgba(40, 40, 40, 1);
			line-height: 64upx;
			text-align: justify;
			margin-top: 40upx;
		}
	}

	.add_input {
		width: 300upx;
		height: 70upx;
		background: rgba(249, 249, 249, 1);
		border: 1px solid rgba(242, 242, 242, 1);
	}

	.collapse_left {
		h4 {
			font-size: 32upx;
			font-weight: 600;
			color: rgba(40, 40, 40, 1);
			line-height: 45upx;
			margin-bottom: 10upx;
		}

		p {
			font-size: 26upx;
			font-weight: 400;
			color: rgba(74, 74, 74, 1);
			line-height: 37upx;
		}

		text {
			font-size: 26upx;
			font-weight: 400;
			color: rgba(155, 155, 155, 1);
			line-height: 37upx;
			text-decoration: underline solid rgba(155, 155, 155, 1);
		}
	}

	.box_size {
		font-size: 0;
		border-top: 1upx solid rgba(242, 242, 242, 0.58);
		padding-top: 40upx;

		h4 {
			font-size: 32upx;
			font-weight: 600;
			color: rgba(40, 40, 40, 1);
			line-height: 45upx;
		}

		text {
			font-size: 26upx;
			font-weight: 400;
			color: rgba(178, 178, 178, 1);
			line-height: 42upx;
			margin-top: 4upx;
		}

		.button_more {
			width: 100%;
			height: 80upx;
			line-height: 80upx;
			box-shadow: 0 -2upx 10upx 0 rgba(0, 0, 0, 0.05);
			font-size: 26upx;
			font-weight: 400;
			background: rgba(240, 240, 240, 0.75);
			margin-top: 40upx;
			color: rgba(155, 155, 155, 1);
			text-align: center;

			image {
				width: 16upx;
				height: 16upx;
				margin-left: 10upx;
				transition: 0.5s;
				-moz-transition: 0.5s;
				/* Firefox 4 */
				-webkit-transition: 0.5s;
				/* Safari and Chrome */
			}

			.image_active {
				transform: rotate(180deg);
				-ms-transform: rotate(180deg);
				/* IE 9 */
				-moz-transform: rotate(180deg);
				/* Firefox */
				-webkit-transform: rotate(180deg);
				/* Safari 和 Chrome */
			}
		}
	}

	.border_fee {
		border-top: 1upx solid rgba(242, 242, 242, 0.58);
		border-bottom: 1upx solid rgba(242, 242, 242, 0.58);
		padding: 16upx 0;
		box-sizing: border-box;
		margin: 40upx 0;

		.total_fee {

			p {
				font-size: 24upx;
				font-weight: 600;
				color: rgba(40, 40, 40, 1);
			}

			text {
				font-size: 36upx;
				font-weight: 600;
				color: rgba(40, 40, 40, 1);
				margin-left: 8upx;
			}
		}
	}


	.total_tip {
		box-sizing: border-box;
		padding: 26upx;
		background: rgba(239, 247, 247, 1);
		border-radius: 10px;

		.total_tip_right {
			width: 100%;

			p {
				text-align: justify;
				font-size: 26upx;
				font-weight: 500;
				color: rgba(84, 140, 140, 1);
				line-height: 40upx;
			}
		}
	}

	.button {
		font-size: 28upx;
		margin: 0;
		font-weight: 400;
		border: 0 none;
		padding: 0;
	}

	.button_left {
		color: rgba(3, 166, 166, 1);
		line-height: 40upx;
	}

	.button_right {
		font-size: 26upx;
		color: rgba(155, 155, 155, 1);
		line-height: 37upx;
		text-decoration: underline solid rgba(155, 155, 155, 1);
	}

	.popup_alert_wrap {
		position: relative;
		width: 707upx;
		height: 80vh;
		background: rgba(255, 255, 255, 1);
		border-radius: 20upx;
		z-index: 999;

		.scroll_content {
			position: absolute;
			top: 90upx;
			left: 0;
			right: 0;
			bottom: 160upx;
			margin: auto;
			padding: 0 30upx;
			box-sizing: border-box;
		}

		.alert_bottom {
			position: absolute;
			left: 0;
			right: 0;
			bottom: 20upx;
			margin: auto;

			.alert_button {
				width: 60%;
				height: 80upx;
				line-height: 80upx;
				box-shadow: 0 -2upx 10upx 0 rgba(0, 0, 0, 0.05);
				font-size: 28upx;
				font-weight: 500;
				color: #FFFFFF;
				background: #3BC1BB;
			}
		}
	}

	.popup_wrap {
		width: 100%;
		height: 1000upx;
		background: rgba(255, 255, 255, 1);
		border-radius: 20upx 20upx 0 0;
		overflow-y: scroll;
	}

	.popup_cont {
		.popup_item {
			box-sizing: border-box;
			padding: 30upx;
		}
	}

	.button_block {
		position: fixed;
		bottom: 0;
		width: 100%;
		height: 90upx;
		line-height: 90upx;
		box-shadow: 0 -2upx 10upx 0 rgba(0, 0, 0, 0.05);
		font-size: 32upx;
		font-weight: 500;
		background-color: #B2B2B2;
		color: #FFFFFF;
	}

	.button_block_active {
		background: #3BC1BB;
	}
</style>
