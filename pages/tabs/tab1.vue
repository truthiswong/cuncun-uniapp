<template>
	<view class="page">
		<uni-nav-bar class="header" status-bar="true" fixed="true" v-if="headerShow" backgroundColor="rgba(0,0,0,0)" style="position: absolute; top: 0;">
			<view slot="left">
				<image src="../../static/tab1/tab1_logo.png" style="width:306upx; height:68upx; margin: 0px 30upx -24upx;"></image>
			</view>
			<view slot="right">
				<view class="header_icon">
					<image @click="onClickRight(1)" style="" src="../../static/tab1/search_white.png"></image>
					<image @click="onClickRight(2)" src="../../static/tab1/add.png"></image>
				</view>
			</view>
		</uni-nav-bar>
		<uni-nav-bar class="header" status-bar="true" fixed="true" v-if="!headerShow" style="position: absolute; top: 0;"
		 shadow="true">
			<view slot="left">
				<image src="../../static/tab1/header_active.png" style="width:374upx; height:48upx; margin: 0px 30upx -14upx;"></image>
			</view>
			<view slot="right">
				<view class="header_icon">
					<image @click="onClickRight(1)" src="../../static/tab1/search_green.png"></image>
					<image @click="onClickRight(2)" src="../../static/tab1/add_green.png"></image>
				</view>
			</view>
		</uni-nav-bar>
		<!-- 内容 -->
		<view class="content" @longpress="longpress">
			<view class="cont_top" :style="{background: 'url('+ cont_top_bg +') no-repeat center center / cover'}">
				<view class="cont_dialog" :style="{background: 'url('+ cont_dialog_bg +') no-repeat center center / cover'}">
					<image style="position: absolute;top: 20upx;left: 20upx; width: 46upx;height: 34upx;" src="../../static/tab1/mark_left.png"></image>
					<view class="cont_dialog_text">
						<h3>{{welcomeText}}</h3>
						<p>我们的执照收纳咨询师和打包小哥正在随时待命中哦!</p>
					</view>
					<image style="position: absolute;right: 45upx; width: 46upx;height: 34upx;" src="../../static/tab1/mark_right.png"></image>
				</view>
			</view>
			<view class="no_data" v-if="failData.length<=0 && bookData.length<=0 && clotheData.length<=0 && shoeData.length<=0 && storageData.length<=0 && groceriesData.length<=0">
				<image src="../../static/tab1/no_data.png" mode=""></image>
				<p>您的存存空空如也，跟我们的收纳达人和打包小哥约起来，赶紧的！！！</p>
				<button @click="onClickRight(3)" class="common_button">约！约！约!</button>
			</view>
			<view v-else>
				<!-- 未过安检的箱子 -->
				<view @longpress="longpress" class="list_margin50" v-if="failData.length>0" style="background-color: #FFFFFF;">
					<view class="list_padding30">
						<uni-list class="list_custom list_custom_img3">
							<uni-list-item thumb="../../static/tab1/box_wrong_title.png" :showArrow="false">
								<view slot="right">
									<navigator url="/pages/tab1/failBox">
										<span style="font-size: 14px;font-weight: 400;color: rgba(59, 193, 187, 1);">查看全部 <image style="width: 16upx;height: 16upx;margin-left: 10upx;"
											 src="../../static/tab1/right.png"></image></span>
									</navigator>
								</view>
							</uni-list-item>
						</uni-list>
					</view>
					<view>
						<view class="box_wrong_content flex_between" :style="{background: 'url('+ box_wrong_bg +') no-repeat center center / cover'}">
							<view class="box_wrong_left">
								<image src="../../static/tab1/box_wrong.png"></image>
							</view>
							<view class="box_wrong_right">
								<view class="flex_between">
									<text>未过安检箱子</text>
									<text>× {{failData.length}}</text>
								</view>
								<text class="box_wrong_text">您有箱子未过安检，请速申请返送。如有疑问请联系客服。</text>
							</view>
						</view>
					</view>
				</view>
				<!-- 书架 -->
				<view @longpress="longpress('书架')" class="list_margin50" v-if="long_active || bookData.length>=0" style="background-color: #FFFFFF;">
					<view v-if="tab1ShowHide.bookShowHideTitle == true">
						<view class="list_padding30">
							<uni-list class="list_custom list_custom_img1">
								<view v-if="long_active" class="list_hide_show">
									<image v-show="tab1ShowHide.bookShowHide == false" @click="isShowHide('bookShow')" src="../../static/tab1/show.png"></image>
									<image v-show="tab1ShowHide.bookShowHide == true" @click="isShowHide('bookHide')" src="../../static/tab1/hide.png"></image>
								</view>
								<uni-list-item thumb="../../static/tab1/books_title.png" :showArrow="false">
									<view slot="right">
										<navigator url="/pages/tab1/book">
											<span style="font-size: 14px;font-weight: 400;color: rgba(59, 193, 187, 1);">查看全部 <image style="width: 16upx;height: 16upx;margin-left: 10upx;"
												 src="../../static/tab1/right.png"></image></span>
										</navigator>
									</view>
								</uni-list-item>
							</uni-list>
						</view>
						<view v-if="tab1ShowHide.bookShowHide == true">
							<scroll-view class="scroll_x" scroll-x="true" @scroll="onScroll">
								<view class="scroll_content" :style="{background: 'url('+ scroll_bg1 +') no-repeat center center / cover'}"
								 style="display: inline-block;" v-for="(item,index) in bookData" :key='index'>
									<image v-if="item.coverPic" :src="item.coverPic" mode="aspectFill"></image>
									<image v-else @click="onClickRight(4)" src="../../static/tab1/add_order.png" mode=""></image>
								</view>
							</scroll-view>
						</view>
					</view>
				</view>
				<!-- 衣柜 -->
				<view @longpress="longpress('衣柜')" class="list_margin50" v-if="long_active || clotheData.length>=0" style="background-color: #FFFFFF;">
					<view v-if="tab1ShowHide.clotheShowHideTitle == true">
						<view class="list_padding30">
							<uni-list class="list_custom list_custom_img1">
								<view v-if="long_active" class="list_hide_show">
									<image v-show="tab1ShowHide.clotheShowHide == false" @click="isShowHide('clotheShow')" src="../../static/tab1/show.png"></image>
									<image v-show="tab1ShowHide.clotheShowHide == true" @click="isShowHide('clotheHide')" src="../../static/tab1/hide.png"></image>
								</view>
								<uni-list-item thumb="../../static/tab1/clothes_title.png" :showArrow="false">
									<view slot="right">
										<navigator url="/pages/tab1/clothes">
											<span style="font-size: 14px;font-weight: 400;color: rgba(59, 193, 187, 1);">查看全部 <image style="width: 16upx;height: 16upx;margin-left: 10upx;"
												 src="../../static/tab1/right.png"></image></span>
										</navigator>
									</view>
								</uni-list-item>
							</uni-list>
						</view>
						<view v-if="tab1ShowHide.clotheShowHide == true">
							<scroll-view class="scroll_x" scroll-x="true" @scroll="onScroll">
								<view class="scroll_content scroll_content2" :style="{background: 'url('+ scroll_bg2 +') no-repeat center top / 100% 200upx'}"
								 v-for="(item,index) in clotheData" :key='index' style="display: inline-block;">
									<image v-if="item.coverPic" :src="item.coverPic" mode="aspectFill"></image>
									<image v-else @click="onClickRight(4)" src="../../static/tab1/add_order.png" mode=""></image>
									<image style="position: absolute;z-index: 5;left: 0;bottom: 0; width: 100%;height: 112upx;" src="../../static/tab1/clothes_box1.png"></image>
								</view>
							</scroll-view>
						</view>
					</view>
				</view>
				<!-- 鞋柜 -->
				<view @longpress="longpress('鞋柜')" class="list_margin50" v-if="long_active || shoeData.length>=0" style="background-color: #FFFFFF;">
					<view v-if="tab1ShowHide.shoeShowHideTitle == true">
						<view class="list_padding30">
							<uni-list class="list_custom list_custom_img1">
								<view v-show="long_active" class="list_hide_show">
									<image v-show="tab1ShowHide.shoeShowHide == false" @click="isShowHide('shoeShow')" src="../../static/tab1/show.png"></image>
									<image v-show="tab1ShowHide.shoeShowHide == true" @click="isShowHide('shoeHide')" src="../../static/tab1/hide.png"></image>
								</view>
								<uni-list-item thumb="../../static/tab1/shoes_title.png" :showArrow="false">
									<view slot="right">
										<navigator url="/pages/tab1/shoes">
											<span style="font-size: 14px;font-weight: 400;color: rgba(59, 193, 187, 1);">查看全部 <image style="width: 16upx;height: 16upx;margin-left: 10upx;"
												 src="../../static/tab1/right.png"></image></span>
										</navigator>
									</view>
								</uni-list-item>
							</uni-list>
						</view>
						<view v-if="tab1ShowHide.shoeShowHide == true">
							<scroll-view class="scroll_x" scroll-x="true" @scroll="onScroll">
								<view class="scroll_content scroll_content2" v-for="(item,index) in shoeData" :key='index' style="display: inline-block;">
									<image style="position: absolute;z-index: 0;left: 0;top: 0; width: 100%;height: 158upx;" src="../../static/tab1/shoes_box2.png"></image>
									<image v-if="item.coverPic" :src="item.coverPic" mode="aspectFill"></image>
									<image v-else @click="onClickRight(4)" src="../../static/tab1/add_order.png" mode=""></image>
									<image style="position: absolute;z-index: 5;left: 0;bottom: 0; width: 100%;height: 127upx;" src="../../static/tab1/shoes_box1.png"></image>
								</view>
							</scroll-view>
						</view>
					</view>
				</view>
				<!-- 储藏室 -->
				<view @longpress="longpress('储藏室')" class="list_margin50" v-if="long_active || storageData.length>=0" style="background-color: #FFFFFF;">
					<view v-if="tab1ShowHide.storageShowHideTitle == true">
						<view class="list_padding30">
							<uni-list class="list_custom list_custom_img2">
								<view v-show="long_active" class="list_hide_show">
									<image v-show="tab1ShowHide.storageShowHide == false" @click="isShowHide('storageShow')" src="../../static/tab1/show.png"></image>
									<image v-show="tab1ShowHide.storageShowHide == true" @click="isShowHide('storageHide')" src="../../static/tab1/hide.png"></image>
								</view>
								<uni-list-item thumb="../../static/tab1/storage_title.png" :showArrow="false">
									<view slot="right">
										<navigator url="/pages/tab1/storage">
											<span style="font-size: 14px;font-weight: 400;color: rgba(59, 193, 187, 1);">查看全部 <image style="width: 16upx;height: 16upx;margin-left: 10upx;"
												 src="../../static/tab1/right.png"></image></span>
										</navigator>
									</view>
								</uni-list-item>
							</uni-list>
						</view>
						<view v-if="tab1ShowHide.storageShowHide == true">
							<scroll-view class="scroll_x" scroll-x="true" @scroll="onScroll">
								<view class="scroll_content scroll_content4" v-for="(item,index) in storageData" :key='index' style="display: inline-block;width: 220upx;height: 220upx;font-size: 0;">
									<image v-if="item.coverPic" :src="item.coverPic" mode="aspectFill"></image>
									<image v-else @click="onClickRight(4)" src="../../static/tab1/add_order.png" mode=""></image>
								</view>
							</scroll-view>
						</view>
					</view>
				</view>
				<!-- 杂货架 -->
				<view @longpress="longpress('杂货架')" class="list_margin50" v-if="long_active || groceriesData.length>=0" style="background-color: #FFFFFF;">
					<view v-if="tab1ShowHide.groceriesShowHideTitle == true">
						<view class="list_padding30">
							<uni-list class="list_custom list_custom_img2">
								<view v-show="long_active" class="list_hide_show">
									<image v-show="tab1ShowHide.groceriesShowHide == false" @click="isShowHide('groceriesShow')" src="../../static/tab1/show.png"></image>
									<image v-show="tab1ShowHide.groceriesShowHide == true" @click="isShowHide('groceriesHide')" src="../../static/tab1/hide.png"></image>
								</view>
								<uni-list-item thumb="../../static/tab1/groceries_title.png" :showArrow="false">
									<view slot="right">
										<navigator url="/pages/tab1/groceries">
											<span style="font-size: 14px;font-weight: 400;color: rgba(59, 193, 187, 1);">查看全部 <image style="width: 16upx;height: 16upx;margin-left: 10upx;"
												 src="../../static/tab1/right.png"></image></span>
										</navigator>
									</view>
								</uni-list-item>
							</uni-list>
						</view>
						<view v-if="tab1ShowHide.groceriesShowHide == true">
							<view class="box_groceries_content flex_between" v-for="(item,index) in groceriesData" :key="index" v-show="index<3">
								<view class="box_groceries_left">
									<image src="../../static/tab1/box_null.png"></image>
									<text>{{index+1}}</text>
								</view>
								<view class="box_groceries_right" style="color: rgba(40,40,40,1);">
									<!-- <view>
										<text>{{item.code}}</text>
									</view> -->
									<text class="box_groceries_text">箱内物品: {{item.remark}}</text>
								</view>
							</view>
						</view>
						<view v-if="groceriesData.length<=0 || tab1ShowHide.groceriesShowHide != true" style="height: 100upx;"></view>
					</view>
				</view>
				<view class="long_button" v-show="long_active">
					<image @click="longpress_cancel" src="../../static/tab1/long_cancel.png"></image>
					<image @click="longpress_comfirm" src="../../static/tab1/long_comfirm.png"></image>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		components: {},
		data() {
			return {
				headerShow: true,
				welcomeText: '',
				cont_top_bg: '../../static/tab1/tab1_top.png',
				cont_dialog_bg: '../../static/tab1/tab1_bubble.png',
				box_wrong_bg: '../../static/tab1/box_wrong_bg.png',
				scroll_bg1: '../../static/tab1/bookbox.png',
				scroll_bg2: '../../static/tab1/clothes_box.png',
				scroll_bg3: '../../static/tab1/shoes_box2.png',
				long_active: false, //长按显示编辑按钮
				failData: [], //未过安检的箱子
				bookData: [], //书架
				clotheData: [], //衣柜
				shoeData: [], //鞋柜
				storageData: [], //储藏室
				groceriesData: [], //杂货架
				tab1ShowHide: {
					bookShowHide: true, //书架
					bookShowHideTitle: true,
					clotheShowHide: true, //衣柜
					clotheShowHideTitle: true,
					shoeShowHide: true, //鞋柜
					shoeShowHideTitle: true,
					storageShowHide: true, //储藏室
					storageShowHideTitle: true,
					groceriesShowHide: true, //杂货架
					groceriesShowHideTitle: true
				},
				user: {}, // 用户信息
			}
		},
		onLoad() {},
		onShow() {
			// #ifdef APP-PLUS
			uni.report('tab1', {
				'describe': '首页'
			})
			// #endif
			if (uni.getStorageSync('loginRealName')) {
				this.getRealNameReturn()
				uni.removeStorage({
					key: 'loginRealName'
				})
			}
			// console.log(uni.getStorageSync('token'))
			this.getGoodsList()
			this.getFailList()
			if (uni.getStorageSync('tab1ShowHide')) {
				this.tab1ShowHide = uni.getStorageSync('tab1ShowHide')
			}
			this.user = uni.getStorageSync('user')
			let nickname = '存存用户'
			let nowHour = new Date().getHours()
			if (this.user.nickName) {
				nickname = this.user.nickName
			} else if (this.user.name) {
				nickname = this.user.name
				this.getUserInfo()
				this.getRealNameReturn()
			} else {
				this.getUserInfo()
			}
			if (nowHour >= 5 && nowHour <= 12) {
				this.welcomeText = `上午好，${nickname}`
			} else if (nowHour > 12 && nowHour <= 18) {
				this.welcomeText = `下午好，${nickname}`
			} else {
				this.welcomeText = `晚上好，${nickname}`
			}
		},
		onPullDownRefresh() {
			this.getGoodsList()
			this.getFailList()
		},
		onPageScroll(options) {
			if (options.scrollTop > 60) {
				this.headerShow = false;
			} else {
				this.headerShow = true;
			}
		},
		onTabItemTap(e) {},
		methods: {
			onClickRight(index) {
				if (index == 1) {
					uni.navigateTo({
						url: '/pages/tab1/search'
					})
				} else if (index == 2) {
					if (this.user.realNameConfirm) {
						uni.navigateTo({
							url: '/pages/tab2/addOrder',
							success: () => {
								// #ifdef APP-PLUS
								uni.report('tab1Addorder', {
									'describe': '首页加号'
								})
								// #endif
							}
						})
					} else {
						uni.showModal({
							title: '提示',
							content: '根据存存服务协议要求，需要先进行实名认证哦！',
							confirmText: '立即认证',
							cancelText: '先看看',
							success: (res) => {
								if (res.confirm) {
									uni.navigateTo({
										url: '/pages/tab3/realName'
									})
								}
							}
						})
					}
				} else if (index == 3) {
					if (this.user.realNameConfirm) {
						uni.navigateTo({
							url: '/pages/tab2/addOrder',
							success: () => {
								// #ifdef APP-PLUS
								uni.report('tab1AddAdd', {
									'describe': '首页约约约'
								})
								// #endif
							}
						})
					} else {
						uni.showModal({
							title: '提示',
							content: '根据存存服务协议要求，需要先进行实名认证哦！',
							confirmText: '立即认证',
							cancelText: '先看看',
							success: (res) => {
								if (res.confirm) {
									uni.navigateTo({
										url: '/pages/tab3/realName'
									})
								}
							}
						})
					}
				} else if (index == 4) {
					uni.navigateTo({
						url: '/pages/tab2/addOrder',
						success: () => {
							// #ifdef APP-PLUS
							uni.report('tab1ListAdd', {
								'describe': '展示架加号'
							})
							// #endif
						}
					})
				}
			},
			longpress(name) {
				this.long_active = true
				this.tab1ShowHide.bookShowHideTitle = true
				this.tab1ShowHide.clotheShowHideTitle = true
				this.tab1ShowHide.shoeShowHideTitle = true
				this.tab1ShowHide.storageShowHideTitle = true
				this.tab1ShowHide.groceriesShowHideTitle = true
			},
			onScroll() {
				this.long_active = false
			},
			isShowHide(type) {
				console.log(type)
				if (type == 'bookShow') {
					this.tab1ShowHide.bookShowHide = true
				} else if (type == 'bookHide') {
					this.tab1ShowHide.bookShowHide = false
				} else if (type == 'clotheShow') {
					this.tab1ShowHide.clotheShowHide = true
				} else if (type == 'clotheHide') {
					this.tab1ShowHide.clotheShowHide = false
				} else if (type == 'shoeShow') {
					this.tab1ShowHide.shoeShowHide = true
				} else if (type == 'shoeHide') {
					this.tab1ShowHide.shoeShowHide = false
				} else if (type == 'storageShow') {
					this.tab1ShowHide.storageShowHide = true
				} else if (type == 'storageHide') {
					this.tab1ShowHide.storageShowHide = false
				} else if (type == 'groceriesShow') {
					this.tab1ShowHide.groceriesShowHide = true
				} else if (type == 'groceriesHide') {
					this.tab1ShowHide.groceriesShowHide = false
				}
				uni.setStorage({
					key: 'tab1ShowHide',
					data: this.tab1ShowHide
				});
			},
			longpress_comfirm() {
				this.long_active = false
				if (this.tab1ShowHide.bookShowHide == true) {
					this.tab1ShowHide.bookShowHideTitle = true
				} else if (this.tab1ShowHide.bookShowHide == false) {
					this.tab1ShowHide.bookShowHideTitle = false
				}
				if (this.tab1ShowHide.clotheShowHide == true) {
					this.tab1ShowHide.clotheShowHideTitle = true
				} else if (this.tab1ShowHide.clotheShowHide == false) {
					this.tab1ShowHide.clotheShowHideTitle = false
				}
				if (this.tab1ShowHide.shoeShowHide == true) {
					this.tab1ShowHide.shoeShowHideTitle = true
				} else if (this.tab1ShowHide.shoeShowHide == false) {
					this.tab1ShowHide.shoeShowHideTitle = false
				}
				if (this.tab1ShowHide.storageShowHide == true) {
					this.tab1ShowHide.storageShowHideTitle = true
				} else if (this.tab1ShowHide.storageShowHide == false) {
					this.tab1ShowHide.storageShowHideTitle = false
				}
				if (this.tab1ShowHide.groceriesShowHide == true) {
					this.tab1ShowHide.groceriesShowHideTitle = true
				} else if (this.tab1ShowHide.groceriesShowHide == false) {
					this.tab1ShowHide.groceriesShowHideTitle = false
				}
				uni.setStorage({
					key: 'tab1ShowHide',
					data: this.tab1ShowHide
				});
			},
			longpress_cancel() {
				this.long_active = false
			},
			// 获取列表
			getGoodsList() {
				this.$http('user/store/show', "GET", '', res => {
					// #ifdef APP-PLUS
					uni.stopPullDownRefresh()
					// #endif
					let data = res.data
					if (data.success) {
						let goodsData = {
							id: '',
							coverPic: ''
						}
						for (let i = 0; i < 10; i++) {
							if (data.data.bookcase.goods.length <= 0 && data.data.armoire.goods.length <= 0 && data.data.shoebox.goods.length <=
								0 && data.data.storeroom.goods.length <= 0 && data.data.sundries.packs.length <= 0) {
								this.bookData = data.data.bookcase.goods //书架
								this.clotheData = data.data.armoire.goods //衣柜
								this.shoeData = data.data.shoebox.goods //鞋柜
								this.storageData = data.data.storeroom.goods //储藏室
								this.groceriesData = data.data.sundries.packs //杂货架
								return
							} else {
								if (!data.data.bookcase.goods[i]) {
									goodsData.id = i
									data.data.bookcase.goods.push(goodsData)
								}
								if (!data.data.armoire.goods[i]) {
									goodsData.id = i
									data.data.armoire.goods.push(goodsData)
								}
								if (!data.data.shoebox.goods[i]) {
									goodsData.id = i
									data.data.shoebox.goods.push(goodsData)
								}
								if (!data.data.storeroom.goods[i]) {
									goodsData.id = i
									data.data.storeroom.goods.push(goodsData)
								}
							}
						}
						this.bookData = data.data.bookcase.goods //书架
						this.clotheData = data.data.armoire.goods //衣柜
						this.shoeData = data.data.shoebox.goods //鞋柜
						this.storageData = data.data.storeroom.goods //储藏室
						this.groceriesData = data.data.sundries.packs //杂货架
					} else {
						uni.showToast({
							icon: 'none',
							title: data.message
						});
					}
				})
			},
			// 获取未过安检的列表
			getFailList() {
				this.$http('user/pack/list?auditStatus=fail', "GET", '', res => {
					// #ifdef APP-PLUS
					uni.stopPullDownRefresh()
					// #endif
					let data = res.data
					if (data.success) {
						this.failData = data.data //未过安检
					} else {
						uni.showToast({
							icon: 'none',
							title: data.message
						});
					}
				})
			},
			getUserInfo() {
				this.$http('user/current', "GET", '', res => {
					let data = res.data
					if (data.success) {
						uni.setStorage({
							key: 'user',
							data: data.data
						});
						let nowHour = new Date().getHours()
						if (data.data.nickName) {
							this.nickname = data.data.nickName
						} else if (data.data.name) {
							this.nickname = data.data.name
						} else {
							this.nickname = '存存用户'
						}
						if (nowHour >= 5 && nowHour <= 12) {
							this.welcomeText = `上午好，${this.nickname}`
						} else if (nowHour > 12 && nowHour <= 18) {
							this.welcomeText = `下午好，${this.nickname}`
						} else {
							this.welcomeText = `晚上好，${this.nickname}`
						}
					} else {
						uni.showToast({
							icon: 'none',
							title: data.message
						});
					}
				})
			},
			getRealNameReturn() {
				// 查询是否实名
				this.$http('user/alipay/user/certify/query', "GET", '', res => {
					console.log('查询是否实名')
				})
			}
		}
	}
</script>
<style>
	page,
	uni-page {
		height: 100%;
	}
</style>
<style lang="scss" scoped>
	.page {
		background-color: #FCFCFC;
		height: 100%;
	}

	.header_icon {
		width: 190upx;
		height: 88upx;
		text-align: left;

		image {
			width: 88upx;
			height: 88upx;
			box-sizing: border-box;
			padding: 22upx;
		}
	}

	.content {
		width: 100%;
		height: 100%;
	}

	.cont_top {
		position: relative;
		width: 100%;
		height: 474upx;
	}

	.cont_dialog {
		position: relative;
		top: 212upx;
		left: 30upx;
		width: 513upx;
		height: 262upx;

		h3 {
			font-size: 36upx;
			font-weight: 600;
			color: rgba(40, 40, 40, 1);
			line-height: 50upx;
			padding: 50upx 40upx 0 30upx;
			overflow: hidden;
			text-overflow: ellipsis;
			white-space: nowrap;
		}

		p {
			font-size: 28upx;
			font-weight: 400;
			color: rgba(136, 136, 136, 1);
			line-height: 46upx;
			padding: 10upx 40upx 0 30upx;
		}
	}

	.no_data {
		text-align: center;
		padding: 116upx 0;

		image {
			width: 338upx;
			height: 326upx;
		}

		p {
			font-size: 28upx;
			font-weight: 400;
			width: 450upx;
			color: rgba(178, 178, 178, 1);
			line-height: 50upx;
			margin: 70upx auto 0;
		}

		.common_button {
			width: 398upx;
			height: 90upx;
			line-height: 90upx;
			background: rgba(59, 193, 187, 1);
			border-radius: 45upx;
			font-size: 30upx;
			font-weight: 500;
			color: white;
			margin: 80upx auto 0;
		}
	}

	.left_icon image {
		width: 184upx;
		height: 36upx;
		margin: 0px 30upx -8upx;
	}

	.right_icon {
		/* width: 184upx; */
		margin-right: 18upx;
	}

	.right_icon text {
		font-size: 28upx;
		font-weight: 400;
		color: rgba(59, 193, 187, 1);
		line-height: 40upx;
		margin-right: 8upx;
	}

	.right_icon image {
		width: 16upx;
		height: 16upx;
	}

	.box_wrong_content {
		position: relative;
		width: 100%;
		height: 234upx;
		background-color: #FFFFFF;
	}

	.box_wrong_left {
		position: relative;
	}

	.box_wrong_left image {
		width: 308upx;
		height: 210upx;
		margin-top: 60upx;
	}

	.box_wrong_left text {
		position: absolute;
		right: 80upx;
		bottom: 70upx;
		font-size: 50upx;
		font-weight: 700;
		color: #90785e;
	}

	.box_wrong_right {
		width: 380upx;
		font-size: 28upx;
		font-weight: 500;
		color: rgba(255, 255, 255, 1);
		line-height: 50upx;
		margin: 20upx 50upx 0 0;
	}

	.box_wrong_text {
		font-weight: 400;
		line-height: 46upx;
		margin-top: 10upx;
		display: -webkit-box;
		-webkit-box-orient: vertical;
		-webkit-line-clamp: 2;
		overflow: hidden;
	}

	.box_groceries_content {
		position: relative;
		width: 100%;
		height: 234upx;
		background-color: #FFFFFF;
	}

	.box_groceries_left {
		position: relative;
	}

	.box_groceries_left image {
		width: 308upx;
		height: 230upx;
		margin-top: 60upx;
	}

	.box_groceries_left text {
		position: absolute;
		right: 80upx;
		bottom: 80upx;
		font-size: 50upx;
		font-weight: 700;
		color: #90785e;
	}

	.box_groceries_right {
		width: 380upx;
		font-size: 28upx;
		font-weight: 500;
		color: rgba(40, 40, 40, 1);
		line-height: 50upx;
		margin: 20upx 50upx 0 0;
	}

	.box_groceries_text {
		font-weight: 400;
		line-height: 46upx;
		margin-top: 10upx;
		color: #4A4A4A;
		display: -webkit-box;
		-webkit-box-orient: vertical;
		-webkit-line-clamp: 3;
		overflow: hidden;
		text-align: justify;
	}

	.scroll_x {
		white-space: nowrap;
		width: 100%;
		background-color: #FFFFFF;
		overflow: auto;
	}

	.scroll_content {
		position: relative;
		width: 285upx;
		height: 285upx;
		text-align: center;
	}

	.scroll_contentbg1 {
		background: url("/static/tab1/bookbox.png") no-repeat center center / cover;

	}

	.scroll_contentbg2 {
		background: url("/static/tab1/clothes_box.png") no-repeat center center;
		background-size: 100%;
	}

	.scroll_contentbg3 {
		background: url("/static/tab1/shoes_box.png") no-repeat center center;
		background-size: 100%;
	}

	.scroll_content image {
		position: absolute;
		left: 0;
		right: 0;
		margin: auto;
		z-index: 3;
		width: 200upx;
		height: 230upx;
	}

	.scroll_content2 image {
		width: 230upx;
		height: 230upx;
	}

	.scroll_content4 {
		width: 200upx;
		height: 200upx;
		box-sizing: border-box;
		padding: 18upx 18upx 0;
		background: rgba(230, 230, 230, 1);
		margin-right: 10upx;

		image {
			width: 184upx;
			height: 184upx;

		}
	}

	.list_margin50 {
		margin: 50upx 0;
	}

	.list_hide_show {
		position: absolute;
		top: -50upx;
		right: 30upx;
		z-index: 1;
	}

	.list_hide_show image {
		width: 166upx;
		height: 100upx;
	}

	.long_button {
		position: fixed;
		right: 0;
		bottom: 0;
		z-index: 999;
	}

	.long_button image {
		width: 218upx;
		height: 120upx;
	}
</style>
