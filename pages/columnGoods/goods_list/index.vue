<template>
	<view :style="viewColor">
		<view class='productList'>
			<view class='search acea-row row-between-wrapper'>
				<!--#ifdef H5-->
				<view v-if='!$wechat.isWeixin()' class="back" @click='set_where(1)'>
					<view class="iconfont icon-xiangzuo"></view>
				</view>
				<!--#endif-->
				<view class='input acea-row row-between-wrapper'><text class='iconfont icon-sousuo'></text>
					<input placeholder='搜索商品名称' placeholder-class='placeholder' confirm-type='search' name="search" :value='where.keyword'
					 @confirm="searchSubmit"></input>
				</view>
				<view style="text-align: right;" class='iconfont' :class='is_switch==true?"icon-pailie":"icon-tupianpailie"' @click='Changswitch'></view>
			</view>
			<view class="nav-wrapper">
				<view class='nav acea-row row-middle'>
					<view class='item' @click="downStatus = !downStatus" :class="{'t-color':downKey>0 && firstKey == 0}">
						{{downMenu[downKey].title}}
						<text v-if="!downStatus" class="iconfont icon-xiala1 spin"></text>
						<text v-else class="iconfont icon-xiala1"></text>
					</view>
					<view class='item' :class="{'t-color':firstKey == 3}" @click='set_where(3)'>
						销量
					</view>
					<view class='item' :class="{'t-color':firstKey == 2}" @click='set_where(2)'>
						价格
						<image v-if="price==1" :src="domain+'/static/diy/up'+keyColor+'.png'"></image>
						<image v-else-if="price==2" :src="domain+'/static/diy/down'+keyColor+'.png'"></image>
						<image v-else :src='`${domain}/static/images/horn.png`'></image>
					</view>
					<!-- down -->
					<view class='item' :class="{'t-color':firstKey == 4}" @click='bindRight'>
						筛选
						<text class="iconfont icon-shaixuan"></text>
					</view>
				</view>	
			</view>
			<!-- 商品 -->
			<block>
				<view v-if="!is_switch" class='list acea-row row-between-wrapper on'>
					<view class='item on' hover-class='none' v-for="(item,index) in productList" :key="index"
					 @click="godDetail(item)">
						<view class='pictrue on'>
							<image :src='item.image' class="on"></image>
							<view v-if="item.border_pic" :style="{ backgroundImage: `url(${item.border_pic})` }" class="border-picture"></view>
						</view>
						<view class='text on'>
							<view class='name line1'>
								<text class="text_name line2">{{item.store_name}}</text>
							</view>
							<view class='money on'>￥<text class='num'>{{item.price}}</text></view>
							<view class="item_tags">
								<text v-if="item.merchant&&item.merchant.type_name && item.product_type == 0" class="font-bg-red bt-color">{{item.merchant.type_name}}</text>
								<text v-else-if="item.merchant.is_trader && item.product_type == 0" class="font-bg-red bt-color">自营</text>
								<text v-if="item.product_type != 0" :class="'font_bg-red bt-color type'+item.product_type">{{item.product_type == 1 ? "秒杀" : item.product_type == 2 ? "预售" : item.product_type == 3 ? "助力" : item.product_type == 4 ? "拼团" : ""}}</text>
								<text class="tags_item ticket" v-if="item.issetCoupon">领券</text>
								<text class="tags_item delivery" v-if="item.delivery_free == 1">包邮</text>
							</view>
							<view class="score">{{item.rate}}评分 {{item.reply_count}}条评论</view>
							<view class="company" v-if="item.merchant" @click.stop="goShop(item.mer_id)">
								<text class="line1">{{item.merchant.mer_name}}</text>
								<view class="flex">
									进店
									<text class="iconfont icon-xiangyou"></text>
								</view>
							</view>
						</view>
						<!-- 返佣 -->
						<block v-if="item.max_extension>0 && (item.product_type == 0 || item.product_type == 2)">
							<view class="foot-bar on">
								最高赚 ¥{{item.max_extension}}
							</view>
						</block>
					</view>
				</view>
				<view v-else class='list'>
					<WaterfallsFlow :wfList='productList' @itemTap="godDetail" :type="1" @goShop="goShop"/>
				</view>
				<view class='noCommodity' v-if="productList.length==0 && where.page > 1">
					<view class='pictrue' style="margin: 60rpx auto;">
						<image :src="`${domain}/static/images/noCart.png`"></image>
						<view>暂无商品，去看点什么吧</view>
					</view>
					<recommend v-if="recommend_switch == 1" :hostProduct="hostProduct" :isLogin="isLogin"></recommend>
				</view>
			</block>
			<rightSlider v-if="rightBox&&brandStataus&&parmasStatus" :status="rightBox&&brandStataus&&parmasStatus" :brandList="brandList" :storeTypeArr="storeTypeArr" :parmasList="parmasList" @confirm="confirm" @close="close" :price_on="where.price_on" :price_off="where.price_off" :activeIndex="activeIndex"></rightSlider>
		</view>
		<view class="down-wrapper" v-if="downStatus">
			<view class="bg" @click="downStatus=false"></view>
			<view class="down-box">
				<view class="down-item" v-for="(item,index) in downMenu" :key="item.key" :class="{'on':index == downKey}" @click="bindDown(item,index)">
					{{item.title}}
					<text v-if="index == downKey" class="iconfont icon-gou"></text>
				</view>
			</view>
		</view>
		<!-- #ifndef H5 -->
		<passwordPopup></passwordPopup>
		<!-- #endif -->
	</view>
</template>

<script>
	// +----------------------------------------------------------------------
	// | CRMEB [ CRMEB赋能开发者，助力企业发展 ]
	// +----------------------------------------------------------------------
	// | Copyright (c) 2016~2024 https://www.crmeb.com All rights reserved.
	// +----------------------------------------------------------------------
	// | Licensed CRMEB并不是自由软件，未经许可不能去掉CRMEB相关版权
	// +----------------------------------------------------------------------
	// | Author: CRMEB Team <admin@crmeb.com>
	// +----------------------------------------------------------------------
	import {
		getProductslist,
		getProductHot,
		getBrandlist,
		getStoreTypeApi,
		getParmasList
	} from '@/api/store.js';
	import recommend from '@/components/recommend';
	import rightSlider from '@/components/rightSlider.vue'
	import WaterfallsFlow from '@/components/WaterfallsFlow/WaterfallsFlow.vue'
	import {initiateAssistApi} from '@/api/activity.js';
	import { configMap } from "@/utils";
	import { mapGetters } from "vuex";
	// #ifndef H5
	import passwordPopup from '@/components/passwordPopup';
	// #endif
	import { goShopDetail } from '@/libs/order.js'
	import { HTTP_REQUEST_URL } from '@/config/app';
	import { toLogin } from '@/libs/login.js';
	const app = getApp();
	export default {
		computed: configMap({hide_mer_status: 1,recommend_switch: 0}, mapGetters(['uid','isLogin','scrollTop','viewColor','keyColor'])),
		components: {
			// #ifndef H5
			passwordPopup,
			// #endif
			recommend,
			rightSlider,
			WaterfallsFlow,
		},
		watch:{
			
		},
		data() {
			return {
				domain: HTTP_REQUEST_URL,
				productList: [],
				list: [],
				storeTypeArr: [], //店铺类型
				parmasList: [],
				is_switch: true,
				where: {
					cate_pid: '',
					order: '',
					price_on: '',
					price_off: '',
					brand_id: '',
					mer_type_id: '',
					keyword:'',
					is_trader: '',
					filter_params: '',
					page:1,
					limit:30
				},
				price: 0,
				stock: 0,
				nows: false,
				loadend: false,
				loading: false,
				loadTitle: '加载更多',
				title: '',
				hostProduct: [],
				hotPage: 1,
				hotLimit: 10,
				hotScroll: false,
				// 筛选框
				rightBox: false,
				brandStataus: false,
				parmasStatus: false,
				//品牌列表
				brandList: [],
				downKey: 0,
				downStatus: false,
				// 下拉菜单
				downMenu: [{
						title: '综合',
						key: 1,
					},
					{
						title: '评分',
						key: 2,
					},
					{
						title: '新品',
						key: 3,
					}
				],
				// 是否第一个
				firstKey:0,
				sotreParam:{
					keyword:'',
					page:1,
					limit:30,
					order:'',
				},
				storeKey:0,
				storeScroll:true,
				activeIndex: 0,
			};
		},
		// 滚动监听
		onPageScroll() {
			uni.$emit('scroll');
		},
		onLoad: function(options) {
			this.$set(this.where, 'cate_pid', options.id || '');
			this.title = options.title || '';
			this.$set(this.where, 'keyword', options.searchValue || '');
			this.get_product_list(true);
			this.get_host_product();
		},
		onReady(){

		},
		mounted: function() {

		},
		methods: {
			// 去店铺
			goShop(id){
				if(this.hide_mer_status != 1){
					uni.navigateTo({
						url:`/pages/store/home/index?id=${id}`
					})
				}
			},
			// 获取店铺类型
			async getStoreType() {
				let temp = []
				getStoreTypeApi()
					.then(res => {
						temp = res.data.map(item => {
							return {
								...item,
								check: false
							}
						})
						// if (this.where.mer_type_id.length > 0) {
						// 	this.where.mer_type_id.forEach((ids, index) => {
						// 		temp.forEach(el => {
						// 			if (ids == el.mer_type_id) {
						// 				el.check = true
						// 			}
						// 		})
						// 	})
						// }
						this.storeTypeArr = temp
					})
					.catch(res => {
						this.$util.Tips({
							title: res
						});
					});
			},
			// 获取参数列表
			async getParmasList(){
				let temp = []
				getParmasList({
					cate_pid:this.where.cate_pid,
					keyword:this.where.keyword
				})
					.then(res => {
						this.parmasStatus = true
						temp = res.data.map(item => {
							return {
								...item,
								check: false,
								showValue: false,
								parmasValue: []
							}
						})
						this.parmasList = temp
					})
					.catch(res => {
						this.$util.Tips({
							title: res
						});
					});
			},
			// 右侧切换
			bindRight(){
				this.price = 0;
				this.firstKey = 4;
				if(!this.brandList.length)this.getBrandlist()
				if(!this.storeTypeArr.length)this.getStoreType()
				if(!this.parmasList.length)this.getParmasList()
				this.rightBox = true
			},
			// 品牌列表
			async getBrandlist(){
				let temp = []
				getBrandlist({
					cate_pid:this.where.cate_pid,
					keyword:this.where.keyword
				}).then(res=>{
					this.brandStataus = true
					temp = res.data.list.map(item=>{
						return {
							...item,
							check:false
						}
					})
					this.brandList = temp
				})
			},
			// 去详情页
			godDetail(item) {
				goShopDetail(item, this.uid).then(res => {
					if (this.isLogin) {
						initiateAssistApi(item.activity_id).then(res => {
							let id = res.data.product_assist_set_id;
							uni.hideLoading();
							uni.navigateTo({
								url: '/pages/activity/assist_detail/index?id=' + id
							});
						}).catch((err) => {
							uni.showToast({
								title: err,
								icon: 'none'
							})
						});
					} else {
						toLogin()
					}
				})
			},
			// 组件确定
			confirm(data,index) {
				let arr1 = [],arr2 = []
				if(data.brandList.length == 0){
					this.where.brand_id = ''
				}else{
					data.brandList.forEach(item =>{
						arr1.push(item.brand_id)
					})
					this.where.brand_id = arr1.toString()
				}
				if(data.typeList.length == 0){
					this.where.mer_type_id = ''
				}else{
					data.typeList.forEach(item =>{
						arr2.push(item.mer_type_id)
					})
					this.where.mer_type_id = arr2.toString()
				}
				this.activeIndex = index
				this.where.filter_params = JSON.stringify(data.parmasList) 
				this.rightBox = data.status
				this.where.price_on = data.price_on
				this.where.price_off = data.price_off
				this.where.is_trader = data.is_trader
				this.loadend = false;
				this.$set(this.where, 'page', 1)
				this.get_product_list(true);
			},
			// 组件关闭
			close() {
				this.rightBox = false
			},
			// 下拉选项
			bindDown(item, index) {
				this.firstKey = 0
				if(index == 0){
					this.where.order = ''
				}else if(index == 1){
					this.where.order = 'rate'
				}else if(index == 2){
					this.where.order = 'is_new'
				}
				this.downKey = index
				this.downStatus = false
				this.loadend = false;
				this.$set(this.where, 'page', 1)
				this.get_product_list(true);
			},
			Changswitch: function() {
				let that = this;
				that.is_switch = !that.is_switch
			},
			searchSubmit: function(e) {
				let that = this;
				that.$set(that.where, 'keyword', e.detail.value);
				that.loadend = false;
				that.$set(that.where, 'page', 1)
				this.get_product_list(true);
			},
			/**
			 * 获取我的推荐
			 */
			get_host_product: function() {
				let that = this;
				if (that.hotScroll) return
				getProductHot(
					that.hotPage,
					that.hotLimit,
				).then(res => {
					that.hotPage++
					that.hotScroll = res.data.length < that.hotLimit
					that.hostProduct = that.hostProduct.concat(res.data.list)
					// that.$set(that, 'hostProduct', res.data)
				});
			},
			//点击事件处理
			set_where: function(e) {
				switch (e) {
					case 1:
					uni.setStorageSync('storeIndex', this.where.cate_pid);
					uni.navigateBack()
					break
					case 2:
						this.firstKey = e
						if (this.price == 0){
							this.price = 1;
							this.where.order = 'price_asc'
						}else if (this.price == 1){
							this.price = 2;
							this.where.order = 'price_desc'
						}else if (this.price == 2){
							this.price = 0;
							this.where.order = ''
						}
						this.$set(this.where, 'page', 1)
						this.get_product_list(true);
						break;
					case 3:
						this.price = 0;
						this.loadend = false;
						this.$set(this.where, 'order', 'sales')
						this.$set(this.where, 'page', 1)
						this.get_product_list(true);
						this.firstKey = e
						break;
				}
				this.loadend = false;
				this.$set(this.where, 'page', 1);
				this.get_product_list(true);
			},
			//查找产品
			get_product_list: function(isPage) {
				let that = this;
				if (that.loadend) return;
				if (that.loading) return;
				if (isPage === true) that.$set(that, 'productList', []);
				that.loading = true;
				that.loadTitle = '';
				getProductslist(that.where).then(res => {
					let list = res.data.list;
					let productList = that.$util.SplitArray(list, that.productList);
					let loadend = list.length < that.where.limit;
					that.loadend = loadend;
					that.loading = false;
					that.loadTitle = loadend ? '已全部加载' : '加载更多';
					that.$set(that, 'productList', productList);
					that.$set(that.where, 'page', that.where.page + 1);
				}).catch(err => {
					that.loading = false;
					that.loadTitle = '加载更多';
				});
			},
		},
		onPullDownRefresh() {

		},
		onReachBottom() {
			if (this.productList.length > 0) {
				this.get_product_list('');
			} else {
				this.get_host_product();
			}
		},
		onPullDownRefresh(){
			// 模拟上拉刷新
			setTimeout(()=>{
				const newList = this.productList.reverse();
				this.productList = newList;
				uni.stopPullDownRefresh();
			},500)
		}
	}
</script>
<style lang="scss" scoped>
	.productList .search {
		width: 100%;
		height: 86rpx;
		padding: 0 20rpx;
		box-sizing: border-box;
		position: fixed;
		left: 0;
		top: 0;
		z-index: 9;
		background-color: var(--view-theme);
	}
	.bt-color{
		background-color: var(--view-theme);
		border:1px solid var(--view-theme);
		&.type2{
			background-color: #FD6523;
			border: 1px solid #FD6523;
		}
	}
	.productList .search .back {
		display: flex;
		align-items: center;
		width: 40rpx;
		height: 60rpx;

		.iconfont {
			color: #fff;
			font-size: 36rpx;
		}
	}

	.productList .search .input {
		width: 570rpx;
		height: 60rpx;
		background-color: #fff;
		border-radius: 50rpx;
		padding: 0 20rpx;
		box-sizing: border-box;
	}

	.productList .search .input input {
		flex: 1;
		height: 100%;
		font-size: 26rpx;
		margin-left: 10rpx;
	}

	.productList .search .input .placeholder {
		color: #999;
	}

	.productList .search .input .iconfont {
		font-size: 35rpx;
		color: #555;
	}

	.productList .search .icon-pailie,
	.productList .search .icon-tupianpailie {
		color: #fff;
		width: 62rpx;
		font-size: 40rpx;
		height: 86rpx;
		line-height: 86rpx;
	}

	.productList .nav-wrapper {
		z-index: 9;
		position: fixed;
		left: 0;
		top: 0;
		width: 100%;
		margin-top: 86rpx;
		background-color: $theme-color;

		.tab-bar {
			display: flex;
			align-items: center;

			.tab-item {
				position: relative;
				flex: 1;
				display: flex;
				justify-content: center;
				align-items: center;
				padding: 8rpx 0 20rpx;
				color: #fff;
				font-size: 28rpx;
				font-weight: bold;

				&::after {
					content: ' ';
					position: absolute;
					left: 50%;
					bottom: 18rpx;
					width: 30rpx;
					height: 3rpx;
					background: transparent;
					transform: translateX(-50%);
				}

				&.on {
					&::after {
						background: #fff;
					}
				}
			}
		}
	}

	.productList .nav {
		height: 86rpx;
		color: #454545;
		font-size: 28rpx;
		background-color: #fff;
		display: flex;
		justify-content: space-between;
		padding: 0 28rpx;
	}

	.productList .nav .item {
		display: flex;
		align-items: center;
		justify-content: center;
		flex: 1;
		&.t-color{
			font-weight: bold;
		}
	}
	.productList .nav .item image {
		width: 15rpx;
		height: 19rpx;
		margin-left: 10rpx;
	}
	.productList{
		padding-bottom: calc(0rpx+ constant(safe-area-inset-bottom)); ///兼容 IOS<11.2/
		padding-bottom: calc(0rpx + env(safe-area-inset-bottom)); ///兼容 IOS>11.2/
	}
	.productList .list {
		padding: 0 20rpx;
		margin-top: 168rpx;

	}
	.productList .list.on {
		background-color: #fff;
		border-top: 1px solid #f6f6f6;
	}

	.noCommodity {
		background-color: #fff;
		padding-bottom: 30rpx;
	}

	.spin {
		display: block;
		transform: rotate(180deg);
		font-size: 36rpx;
	}

	.icon-xiala1 {
		margin-top: 6rpx;
	}

	.icon-shaixuan {
		font-size: 30rpx;
		margin-left: 10rpx;
	}

	.down-wrapper {
		.bg {
			z-index: 8;
			position: fixed;
			left: 0;
			top: 0;
			width: 100%;
			height: 100%;
			background: rgba(0, 0, 0, .5);
		}

		.down-box {
			z-index: 8;
			position: fixed;
			left: 0;
			top: 170rpx;
			width: 100%;
			padding: 0 40rpx 28rpx 74rpx;
			background-color: #F8F8F8;
			border-radius: 0 0 24rpx 24rpx;
			.down-item {
				margin-top: 28rpx;
				font-size: 24rpx;
				color: #454545;
				&.on {
					color: var(--view-theme);
				}
				.iconfont{
					float: right;
				}
			}
		}
	}

	.store-wrapper {
		margin-top: 240rpx;
		border-top: 1px solid #F6F6F6;

		.star-box {
			display: flex;
			align-items: center;

			.star {
				position: relative;
				width: 111rpx;
				height: 19rpx;
				background: url(~pages/columnGoods/images/star.png);
				background-size: 111rpx 19rpx;
			}

			.star-active {
				position: absolute;
				left: 0;
				top: 0;
				width: 111rpx;
				height: 19rpx;
				overflow: hidden;
				background: url(~pages/columnGoods/images/star_active.png);
				background-size: 111rpx 19rpx;
			}

			.num {
				color: $theme-color;
				font-size: 24rpx;
				margin-left: 10rpx;
			}
		}

		.store-item {
			margin-bottom: 12rpx;
			padding: 40rpx 20rpx;
			background-color: #fff;

			.head {
				display: flex;
				align-items: center;
				justify-content: space-between;

				.left-wrapper {
					display: flex;
					align-items: center;

					.logo {
						width: 80rpx;
						height: 80rpx;

						image {
							width: 80rpx;
							height: 80rpx;
							border-radius: 6rpx;
						}
					}

					.con-box {
						margin-left: 20rpx;

						.name {
							font-size: 30rpx;
							color: #333;
							font-weight: bold;
						}
					}
				}

				.link {
					width: 114rpx;
					height: 50rpx;
					line-height: 50rpx;
					background: linear-gradient(-90deg, rgba(246, 122, 56, 1) 0%, rgba(241, 27, 9, 1) 100%);
					border-radius: 25rpx;
					text-align: center;
					color: #fff;
					font-size: 24rpx;
				}
			}

			.pic-wrapper {
				display: flex;
				margin-top: 30rpx;

				.pic-item {
					position: relative;
					width: 226rpx;
					height: 226rpx;
					margin-right: 16rpx;

					image {
						width: 226rpx;
						height: 226rpx;
						border-radius: 16rpx;
					}

					.price {
						position: absolute;
						right: 0;
						bottom: 0;
						height: 36rpx;
						padding: 0 10rpx;
						line-height: 36rpx;
						text-align: center;
						background: rgba(0, 0, 0, .5);
						border-radius: 16rpx 2rpx 16rpx 2rpx;
						color: #fff;
						font-size: 24rpx;

						text {
							font-size: 18rpx;
						}
					}

					&:nth-child(3n) {
						margin-right: 0;
					}
				}
			}
		}
	}
	.productList .list .item.on {
		width: 100%;
		display: flex;
		border-bottom: 1px solid #f6f6f6;
		padding: 30rpx 0;
		margin: 0;
		position: relative;
	}
	.productList .list .item .pictrue.on {
		position: relative;
		width: 260rpx;
		height: 260rpx;
		.border-picture {
			position: absolute;
			top: 0;
			left: 0;
			width: 100%;
			height: 100%;
			background: center/cover no-repeat;
		}
	}
	.productList .list .item .pictrue image {
		width: 260rpx;
		height: 260rpx;
		border-radius: 20rpx 20rpx 0 0;
	}
	.productList .list .item .pictrue image.on {
		border-radius: 6rpx;
	}
	.productList .list .item .text {
		font-size: 30rpx;
		color: #222;
		width: 460rpx;
		padding: 0 0 0 22rpx;
		.name{
			height: 80rpx;
		}
	}
.item_tags{
	margin-top: 8rpx;
}
.item_tags .tags_item {
	display: inline-block;
	font-size: 20rpx;
	text-align: center;
	border-radius: 5rpx;
	padding: 0 4rpx;
	line-height: 28rpx;
	margin-right: 8rpx;
}
.item_tags .tags_item.ticket{
	color: #E93323;
	border: 1px solid #E93323;
}
.item_tags .tags_item.delivery{
	color: #FF9000;
	border: 1px solid #FF9000;
}
	.productList .list .item .text .money {
		display: flex;
		align-items: center;
		font-size: 26rpx;
		font-weight: bold;
		margin-top: 10rpx;
		color: var(--view-priceColor);
	}
	.productList .list .item .text .money .num {
		font-size: 34rpx;
	}
	.productList .distance {
		display: flex;
		align-items: center;
		font-size: 24rpx;
		color: #999999;

		.icon-xiangyou {
			font-size: 24rpx;
			line-height: 24rpx;
		}
	}

	.productList .list .item .text .money .ticket-big {
		display: flex;
		align-items: center;
		justify-content: center;
		min-width: 163rpx;
		height: 28rpx;
		margin-left: 10rpx;
		background-image: url(~static/images/yh.png);
		background-size: 100% 100%;
		font-size: 20rpx;
		font-weight: normal;
		padding: 0 10rpx;
	}
	.productList .list .item .text .score {
		margin-top: 10rpx;
		color: #737373;
		font-size: 20rpx;
	}

	.productList .list .item .text .company {
		display: flex;
		align-items: center;
		color: #737373;
		font-size: 20rpx;
		margin-top: 10rpx;

		.name {
			display: inline-block;
			width: 200rpx;
			overflow: hidden;
			white-space: nowrap;
			text-overflow: ellipsis;
		}
		.flex {
			display: flex;
			align-items: center;
			margin-left: 10rpx;
			color: #282828;
			.iconfont {
				font-size: 16rpx;
				margin-top: 4rpx;
			}
		}
	}
	.productList .list .item .foot-bar {
		width: 100%;
		height: 52rpx;
		display: flex;
		align-items: center;
		justify-content: center;
		background-image: linear-gradient(-90deg, var(--view-bntColor21) 0%, var(--view-bntColor22) 100%);
		border-radius: 0px 0px 16rpx 16rpx;
		color: #fff;
		font-size: 24rpx;
		.icon-fenxiang {
			font-size: 24rpx;
			margin-right: 10rpx;
		}
		&.on {
			position: absolute;
			right: 0;
			bottom: 30rpx;
			width: 180rpx;
			height: 44rpx;
			border-radius: 22rpx;
		}
	}
</style>
