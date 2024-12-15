d<template>
	<view>
		
		<view class='site'>
			<view>
				<view class="navbar">
					<view class="body">
						<view class="header">
							<view><span>站点</span></view>
							<view class="badge">
								<view class="uni-badge--x">
									<view class="noticeIcon">
										<img src="/static/images/user_remind.png" style="width: 21px; height: 21px;" draggable="false">
									</view>
								</view>
							</view>
						</view>
					</view>
				</view>
				<view class="navbarEmpty">
					<view class="body"></view>
				</view>
			</view>
			<view class="n-header">
				<view class="input">
					<view class="search">
						<img src="/static/images/index_search.png" style="width: 22px;height: 22px;margin-right: 6px;" draggable="false">
					</view>
					<uni-view class="text-line-1" style="max-width: 85%;">输入关键词，商品ID或网址</uni-view>
				</view>
			</view>
			<view class="z-paging-content z-paging-content-full z-paging-reached-top">
				<view class="zp-view-super zp-scroll-view-super">
					<view class="zp-scroll-view-container">
						<view class="zp-scroll-view zp-scroll-view-absolute">
							<view class="zp-scroll-view">
								<view class="zp-scroll-view" style="overflow: hidden auto;">
									<view class="uni-scroll-view-content">
										
										<view class="zp-paging-container">
											<view class="zp-paging-container" :style="viewColor">	
												<view class="zp-paging-container-content">
													<view class="scroll_view">
														<view class="content">
															<view class="item" id="site_mercari">
																<view class="imgBox">
																	<view class="image">
																		<img src="/static/images/mercari.png" class="imageBox-image" draggable="false">
																	</view>
																	<view class="introduce">日本最大的闲置交易平台</view>
																</view>
															</view>
															<view class="item" id="site_yahu">
																<view class="imgBox">
																	<view class="image">
																		<img src="/static/images/yahu.png" class="imageBox-image" draggable="false">
																	</view>
																	<view class="introduce">雅虎旗下的二手交易平台</view>
																</view>
															</view>
															<view class="item" id="site_junhe">
																<view class="imgBox">
																	<view class="image">
																		<img src="/static/images/junhe.png" class="imageBox-image" draggable="false">
																	</view>
																	<view class="introduce">二次元爱好者的天堂</view>
																</view>
															</view>
														</view>
													</view>
												</view>
											</view>
										</view>
										
									</view>
								</view>
							</view>
						</view>
					</view>
				</view>
			</view>
			
			
			<!-- #ifndef H5 -->
			<passwordPopup></passwordPopup>
			<!-- #endif -->
		</view>
		<!--自定义底部tab栏-->
		<customTab :newData="newData" :activeRouter="activeRouter"></customTab>
	</view>
</template>
<script>
	let app = getApp();
	import { getCategoryList } from '@/api/store.js';
	import { getNavigation } from '@/api/public.js'
	import { configMap } from '@/utils/index';
	// #ifndef H5
	import passwordPopup from '@/components/passwordPopup';
	// #endif
	import easyLoadimage from '@/components/easy-loadimage/easy-loadimage.vue';
	import customTab from '@/components/customTab';
	import { mapGetters } from "vuex";
	import { HTTP_REQUEST_URL } from '@/config/app';
	export default {
		components: {
			easyLoadimage,
			customTab,
			// #ifndef H5
			passwordPopup,
			// #endif
		},
		computed: configMap({navigation: {}},mapGetters(['viewColor','keyColor'])),
		data() {
			let active = 0;
			// #ifdef H5
			// active = location.hash.substr(1);
			// #endif
			return {
				domain: HTTP_REQUEST_URL,
				showSkeleton: true, //骨架屏显示隐藏
				isNodes: 0, //控制什么时候开始抓取元素节点,只要数值改变就重新抓取
				scrollTop: 0,
				navlist: [],
				hotList: [], //推荐分类
				productList: [
					{cate_name: 'skeleton',store_category_id: 0,
					children:[
						{cate_name: 'skeleton',store_category_id: 10,children:[
							{cate_name: '',store_category_id: 101,},
							{cate_name: '',store_category_id: 102,},
							{cate_name: '',store_category_id: 103,},
							{cate_name: '',store_category_id: 104,},
							{cate_name: '',store_category_id: 105,},
							{cate_name: '',store_category_id: 106,}
						]}]
					},
					{cate_name: 'skeleton',store_category_id: 2,
					children:[{cate_name:'skeleton',store_category_id: 30}]},
					{cate_name: 'skeleton',store_category_id: 3,
					children:[{cate_name:'skeleton',store_category_id: 31}]},
					{cate_name: 'skeleton',store_category_id: 4,
					children:[{cate_name:'skeleton',store_category_id: 32}]},
					{cate_name: 'skeleton',store_category_id: 5,
					children:[{cate_name:'skeleton',store_category_id: 33}]},
					{cate_name: 'skeleton',store_category_id: 6,
					children:[{cate_name:'skeleton',store_category_id: 34}]},
					{cate_name: 'skeleton',store_category_id: 7,
					children:[{cate_name:'skeleton',store_category_id: 35}]},
					{cate_name: 'skeleton',store_category_id: 8,
					children:[{cate_name:'skeleton',store_category_id: 36}]},
					{cate_name: 'skeleton',store_category_id: 9,
					children:[{cate_name:'skeleton',store_category_id: 37}]},
					{cate_name: 'skeleton',store_category_id: 10,
					children:[{cate_name:'skeleton',store_category_id: 38}]},
					{cate_name: 'skeleton',store_category_id: 11,
					children:[{cate_name:'skeleton',store_category_id: 39}]},
				],
				cateList: [
					{cate_name: 'skeleton',store_category_id: 10,children:[
						{cate_name: '',store_category_id: 101,},
						{cate_name: '',store_category_id: 102,},
						{cate_name: '',store_category_id: 103,},
						{cate_name: '',store_category_id: 104,},
						{cate_name: '',store_category_id: 105,},
						{cate_name: '',store_category_id: 106,}
					]},	
				],
				navActive: 0,
				activceCate: active,
				number: "",
				height: 0,
				hightArr: [],
				winHeight: 0,
				pidIndex: 0,
				intoindex: "",
				pid: '',
				newData: {},
				activeRouter: '',
			}
		},
		onLoad(options) {
			let that = this	
			that.getAllCategory();
			// if(options.activceCate){
			// 	this.activceCate = options.activceCate;
			// }else if(index){
			// 	this.activceCate = index;
			// 	uni.removeStorageSync('storeIndex');
			// }
			
			uni.getSystemInfo({
				success: function(res) {
					that.winHeight = res.windowHeight - 2
				},
			});
			// #ifdef H5
			document.body.addEventListener('touchmove', function(event) {
				if (that.$route.path == '/pages/goods_cate/goods_cate') {
					event.preventDefault();
				}
			}, {
				passive: false
			});
			// #endif
		
		},
		/**
		 * 用户点击右上角分享
		 */
		// #ifdef MP
		onShareAppMessage: function() {
			wx.showShareMenu({
				withShareTicket: true,
				menus: ['shareAppMessage', 'shareTimeline']
			})
			return {
				path: '/pages/goods_cate/goods_cate?activceCate=' + this.productList[this.navActive] ? this.productList[this.navActive].store_category_id : 0
			}
		},
		onShareTimeline: function() {
			return {
				query: {
					activceCate: this.productList[this.navActive] ? this.productList[this.navActive].store_category_id : 0,
				}
			}
		},
		// #endif
		onShow() {
			let that = this
			let routes = getCurrentPages();
			let curRoute = routes[routes.length - 1].route
			this.activeRouter = '/' + curRoute
			// let pid = uni.getStorageSync('storeIndex')
			// if(pid) {
			// 	this.activceCate = pid
			// 	uni.removeStorageSync('storeIndex');
			// }
			// if(this.activceCate){
			// 	this.getCateFrom(this.productList)
			// 	pid && setTimeout(()=>this.tap(this.pidIndex, this.activceCate), 200);
			// }
			
			that.getNav()
		},
		onHide() {},
		// //点击底部tabbar调用
		// onTabItemTap() {
		// 	this.getAllCategory();
		// },
		onReady() {
			this.isNodes++;
		},	
		methods: {
			getNav() {
				getNavigation().then(res => {
					this.newData = res.data
					if (this.newData.status && this.newData.status.status) {
						uni.hideTabBar()
					} else {
						uni.showTabBar()
					}
				})	
			},
			tap: function(item,index) {
				this.cateList= item['children'];
				this.navActive = index ;
			},
			getAllCategory: function() {
				let that = this;
				let value = ""
				that.pidIndex = 0;
				getCategoryList().then(res => {
					that.productList = res.data.list;
					that.hotList = res.data.hot;
					if(that.hotList.length>0){
						let hot = {
							cate_name: '推荐分类',store_category_id: 0,
							children: [
								{
									cate_name:'推荐分类',
									store_category_id: 1,
									children:that.hotList
								}
							]
						}
						that.productList.unshift(hot)
						
					}
					that.cateList = that.productList[0]['children']
					that.getCateFrom(that.productList)
					uni.stopPullDownRefresh(); //结束下拉刷新
					setTimeout(() => {
						that.showSkeleton = false
					}, 500)
					const index = uni.getStorageSync('storeIndex');
					if(index){
						uni.removeStorageSync('storeIndex');
						for(var i = 0; i < that.productList.length; i++){
							if(that.productList[i].store_category_id == index ){
								that.tap(that.productList[i], i)
								return;
							}
						}
					}
				})
			},
			//获取首页分类来源
			getCateFrom: function(arr) {
				arr.map((item, index) => {
					if ((this.activceCate && item.store_category_id == this.activceCate)) {
						this.pidIndex = index
						this.navActive = index
						this.tap(item,index)
						// i = 'sort' + index
						return;
					}
				})

			},
			searchSubmitValue: function(e) {
				if (this.$util.trim(e.detail.value).length > 0)
					uni.navigateTo({
						url: '/pages/columnGoods/goods_list/index?searchValue=' + e.detail.value
					})
				else
					return this.$util.Tips({
						title: '请填写要搜索的产品信息'
					});
			},
		},
		onPullDownRefresh: function(){
			this.getAllCategory();
		},
		// 滚动监听
		onPageScroll({scrollTop}) {
			// 传入scrollTop值并触发所有easy-loadimage组件下的滚动监听事件
			this.scrollTop = scrollTop;
		}
	}
</script>
<style scoped lang="scss">
	.site {
			background-color: #fff;
			background-image: url(/static/images/common_bg.png);
			background-repeat: no-repeat;
			background-size: 100% 248px;
			height: 100dvh;
		}
		
		.site .navbar {
			background-color: rgba(0, 0, 0, 0);
			height: 41px;
			min-height: 41px;
			padding-top: 0px;
			z-index: 20;
			position: fixed;
			border-bottom: unset;
			width: 100%;
		}
		.site .navbar .body {
			display: block;
			height: 41px; 
			min-height: 41px;
		}
		.site .navbar .body .header{
			width: 100%;
		    display: flex;
		    flex-direction: row;
		    align-items: center;
		    position: relative;
		    justify-content: center;
		    font-size: 17px;
		    font-weight: 700;
		    color: #333;
			min-height: 41px;
		}
		.site .navbar .body .header .badge{
			position: absolute;
			right: 0;
			display: flex;
			flex-direction: row;
			align-items: center;
			justify-content: center;
			width: 26px;
			height: 26px;
			padding: 4px 4px 0 0;
			margin-bottom: 4px;
			margin-right: 13px;
		}
		
		.site .navbar .body .header .uni-badge--x{
			display: inline-block;
			position: relative;
		}
		
		.site .navbar .body .header .badge .noticeIcon{
			background-image: url("/static/images/user_remind.png"); 
			background-position: center center; 
			background-size: contain; 
			background-repeat: no-repeat;
		}
		.site .navbarEmpty {
			height: 41px; 
			min-height: 41px;
		}
		.site .n-header {
			width: 100%;
			padding: 0 14px;
			display: flex;
			flex-direction: row;
			align-items: center;
			height: 43px;
			margin-top: 8px;
		}
		
		.site .n-header .input {
			display: flex;
			    align-items: center;
			    background-color: rgba(51,51,51,.06);
			    border-radius: 8px;
			    color: #aaa;
			    font-size: 14px;
			    flex: 1;
			    padding: 0 10px;
			    border-radius: 8px;
			    height: 100%;
			    transition: background-color .2s ease-in-out
		}
		
		.site .n-header .search {
			width: 22px;
			height: 22px;
			margin-right: 6px;
			background-image: url("/static/h5/new_images/index_search.png"); 
			background-position: center center; 
			background-size: contain;
			background-repeat: no-repeat;
		}
		
		
		.site .z-paging-content {
			margin-top: 14px;
			position: relative;
			flex-direction: column;
			overflow: hidden;
		}
		
		.site .z-paging-content-full {
			display: flex;
			width: 100%;
			height: 100%;
		}
		
		.site .z-paging-reached-top {
			
		}
		
		.site .zp-view-super  { 
			display: flex;
			flex-direction: row;
		}
		
		.site .zp-scroll-view-super {
			flex: 1;
			overflow: hidden;
			position: relative;
		}
		.site .zp-paging-touch-view {
			width: 100%;
			height: 100%;
			position: relative;
		}
		
		.site .zp-scroll-view-container {
			position: relative;
			height: 100%;
			width: 100%;
		}
		
		.site .zp-scroll-view {
			height: 100%;
			width: 100%;
		}
		
		.site .zp-scroll-view-absolute {
			position: absolute;
			top: 0;
			left: 0;
		}
		
		
		
		
		
		
		
		
		.site .scroll_view {
			position: relative;
			margin-top: 11px;
		}
		
		.site .content {
			display: flex;
			flex-wrap: wrap;
			padding: 0 13px;
			padding-top: 1px;
		}
		
		.site .item {
			position: relative;
		    width: calc(100% / 2 - 4px);
		    margin-bottom: 9px;
			margin-left: 4px;
		}
		
		.site .imgBox {
			width: 100%;
			overflow: hidden;
			border-radius: 8px;
			position: relative;
		}
		
		.site .imgBox .image{
			background-position: center center; 
			background-size: contain; 
			background-repeat: no-repeat;
			width: 100%;
			height: 100%;
			transition: all .2s ease-in-out;
		}

		.site .imgBox .imageBox-image{
			width: 100%;
			height: 100%;
		}
		
		.site .imgBox .introduce{
			position: absolute;
			top: 72px;
			left: 0;
			width: 100%;
			color: #fff;
			text-align: center;
			font-size: 13px;
		}
		
		
		.page-footer {
			position: fixed;
			bottom: 0;
			z-index: 30;
			display: flex;
			align-items: center;
			justify-content: space-around;
			width: 100%;
			height: calc(98rpx+ constant(safe-area-inset-bottom)); ///兼容 IOS<11.2/
			height: calc(98rpx + env(safe-area-inset-bottom)); ///兼容 IOS>11.2/
			box-sizing: border-box;
			border-top: solid 1rpx #F3F3F3;
			background-color: #fff;
			box-shadow: 0px 0px 17rpx 1rpx rgba(206, 206, 206, 0.32);
			padding-bottom: constant(safe-area-inset-bottom); ///兼容 IOS<11.2/
			padding-bottom: env(safe-area-inset-bottom); ///兼容 IOS>11.2/
			.foot-item {
				display: flex;
				width: max-content;
				align-items: center;
				justify-content: center;
				flex-direction: column;
				position: relative;
				.count-num {
					position: absolute;
					display: flex;
					justify-content: center;
					align-items: center;
					width: 40rpx;
					height: 40rpx;
					top: 0rpx;
					right: -15rpx;
					color: #fff;
					font-size: 20rpx;
					background-color: #FD502F;
					border-radius: 50%;
					padding: 4rpx;
				}
			}
			.foot-item image {
				height: 50rpx;
				width: 50rpx;
				text-align: center;
				margin: 0 auto;
			}
			.foot-item .txt {
				font-size: 24rpx;
			}
		}
</style>