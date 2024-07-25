<template>
	<view v-if="plantList.length > 0" :style="'margin-top:'+mbConfig+'rpx;'">
		<view class="plant-count skeleton-rect" :style="'margin: 0 '+prConfig+'rpx;border-radius:'+bgStyle+'rpx'">
			<block v-if="community_status">
				<view class="spike-bd plant_bg" :style="{ 'background-image': `url(${domain}/static/images/plant_bg.png)`}">
					<view class="title line1"><image class="title-img" :src="`${domain}/static/images/plant_title.png`"></image></view>
					<navigator v-if="!merId" :open-type="open_grass ? 'switchTab' : 'navigate'" url="/pages/plant_grass/index" class="more-btn" hover-class="none">
						
						<text class="iconfont icon-jiantou" hover-class="none"></text>
					</navigator>
				</view>
				<view class="live-wrapper plant" style="border-radius: 0;">
					<scroll-view :class="'colum'+styleType" :scroll-x="styleType == 0 ? true : false" >
						<view 
						v-for="(item, index) in plantList" 
						:key="index" 
						class="item" 
						:class="'plant-item'+styleType"
						:style="'border-radius:'+conStyle+'rpx'"
						@click="goDetail(item)"
						>
							<view :class="'img-box'+conStyle">
								<easy-loadimage mode="widthFix" :image-src="item.image[0]"></easy-loadimage>
								<view class="item_text">
									<text v-if="titleShow" class="text_name line1">{{item.title}}</text>
									<view v-if="avatarShow || nicknameShow" class="text_count acea-row">
										<image v-if="avatarShow" :src="(item.author && item.author.avatar) || '/static/images/f.png'" ></image>
										<text v-if="nicknameShow" class="name line1">{{(item.author && item.author.nickname) || ''}}</text>
									</view>
									<view class="like_count" v-if="styleType == 1">
										<text class="iconfont" :class="item.relevance_id ? 'icon-shoucang1' : 'icon-dianzan'"></text>
										<text class="collect">{{item.count_start}}</text>
									</view>
								</view>
							</view>
						</view>
					</scroll-view>
				</view>
			</block>
		</view>
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
	
import { HTTP_REQUEST_URL } from '@/config/app';	
import easyLoadimage from '@/components/easy-loadimage/easy-loadimage.vue';
import { graphicLstData } from '@/api/api.js';
import { openPlantGrass } from "@/config/app.js";
import { mapGetters } from 'vuex';
import { configMap } from '@/utils';
export default {
	computed: configMap(['community_status']),
	components:{
		easyLoadimage
	},
	name: 'plantList',
	props: {
		dataConfig: {
			type: Object,
			default: () => {}
		},
		merId: {
			type: String || Number,
			default: ''
		}
	},
	data() {
		return {
			plantList: [],
			open_grass: openPlantGrass,
			mbConfig: this.dataConfig.mbConfig.val*2, //页面间距
			styleType: this.dataConfig.tabConfig.tabVal, //单行，多行，板块
			prConfig: this.dataConfig.prConfig.val*2, //背景间距
			bgStyle: this.dataConfig.bgStyle.type ? 16 : 0,
			conStyle: this.dataConfig.conStyle.type ? 16 : 0,
			titleShow: this.dataConfig.titleShow.val,
			avatarShow: this.dataConfig.avatarShow.val,
			nicknameShow: this.dataConfig.nicknameShow.val,
			domain: HTTP_REQUEST_URL,
			diy_id: this.dataConfig.did,
			unique: this.dataConfig.timestamp,
		};
	},
	created() {},
	mounted() {
		this.getPlantList()
	},
	methods: {
		// 种草
		getPlantList(){
			let that = this;
			graphicLstData({
				diy_id: that.diy_id,
				unique: that.unique,
				mer_id: that.merId,
				limit: 6
			}).then(res => {
					//that.plantList = res.data.list;
					that.plantList = [
            {
                "community_id": 33,
                "title": "モモンガ　おしりふりふりBIGぬいぐるみ",
                "image": [
                    "/static/images/m58824770987_1.webp"
                ],
                "category_id": 64,
                "topic_id": 36,
                "uid": 10917,
                "count_start": 0,
                "count_reply": 0,
                "count_share": 0,
                "status": 1,
                "is_show": 1,
                "start": 1,
                "create_time": "2024-07-03 11:33:31",
                "is_del": 0,
                "content": "モモンガ　おしりふりふりBIGぬいぐるみ",
                "refusal": "",
                "is_hot": 0,
                "order_id": 0,
                "is_type": 1,
                "video_link": "",
                "pv": 19,
                "update_time": null,
                "status_time": "2024-07-03 11:33:42",
                "author": {
                    "uid": 10917,
                    "real_name": "",
                    "status": 1,
                    "avatar": "/static/images/head.jpg",
                    "nickname": "haoku用户",
                    "count_start": 0
                }
            },
            {
                "community_id": 33,
                "title": "ポケットモンスター　ぬいぐるみ　モルペコ（まんぷくもよう）　タグ付き",
                "image": [
                    "/static/images/m64513907513_1.webp"
                ],
                "category_id": 76,
                "topic_id": 41,
                "uid": 10917,
                "count_start": 0,
                "count_reply": 0,
                "count_share": 0,
                "status": 1,
                "is_show": 1,
                "start": 1,
                "create_time": "2024-06-29 11:16:14",
                "is_del": 0,
                "content": "あべっ子ラムネ マスコット ガチャ",
                "refusal": "",
                "is_hot": 0,
                "order_id": 0,
                "is_type": 1,
                "video_link": "",
                "pv": 12,
                "update_time": null,
                "status_time": "2024-06-29 11:16:19",
                "author": {
                    "uid": 10917,
                    "real_name": "",
                    "status": 1,
                    "avatar": "/static/images/head.jpg",
                    "nickname": "haoku用户",
                    "count_start": 0
                }
            },
            {
                "community_id": 32,
                "title": "vあべっ子ラムネ マスコット ガチャ",
                "image": [
                    "/static/images/m54849051782_1.webp"
                ],
                "category_id": 16,
                "topic_id": 31,
                "uid": 10917,
                "count_start": 0,
                "count_reply": 0,
                "count_share": 0,
                "status": 1,
                "is_show": 1,
                "start": 1,
                "create_time": "2024-06-29 11:15:25",
                "is_del": 0,
                "content": "あべっ子ラムネ マスコット ガチャ",
                "refusal": "",
                "is_hot": 0,
                "order_id": 0,
                "is_type": 1,
                "video_link": "",
                "pv": 5,
                "update_time": null,
                "status_time": "2024-06-29 11:15:46",
                "author": {
                    "uid": 10917,
                    "real_name": "",
                    "status": 1,
                    "avatar": "/static/images/head.jpg",
                    "nickname": "haoku用户",
                    "count_start": 0
                }
            },
            {
                "community_id": 32,
                "title": "vあべっ子ラムネ マスコット ガチャ",
                "image": [
                    "/static/images/m54849051782_1.webp"
                ],
                "category_id": 16,
                "topic_id": 31,
                "uid": 10917,
                "count_start": 0,
                "count_reply": 0,
                "count_share": 0,
                "status": 1,
                "is_show": 1,
                "start": 1,
                "create_time": "2024-06-29 11:15:25",
                "is_del": 0,
                "content": "あべっ子ラムネ マスコット ガチャ",
                "refusal": "",
                "is_hot": 0,
                "order_id": 0,
                "is_type": 1,
                "video_link": "",
                "pv": 5,
                "update_time": null,
                "status_time": "2024-06-29 11:15:46",
                "author": {
                    "uid": 10917,
                    "real_name": "",
                    "status": 1,
                    "avatar": "/static/images/head.jpg",
                    "nickname": "haoku用户",
                    "count_start": 0
                }
            },
            {
                "community_id": 32,
                "title": "vあべっ子ラムネ マスコット ガチャ",
                "image": [
                    "/static/images/m54849051782_1.webp"
                ],
                "category_id": 16,
                "topic_id": 31,
                "uid": 10917,
                "count_start": 0,
                "count_reply": 0,
                "count_share": 0,
                "status": 1,
                "is_show": 1,
                "start": 1,
                "create_time": "2024-06-29 11:15:25",
                "is_del": 0,
                "content": "あべっ子ラムネ マスコット ガチャ",
                "refusal": "",
                "is_hot": 0,
                "order_id": 0,
                "is_type": 1,
                "video_link": "",
                "pv": 5,
                "update_time": null,
                "status_time": "2024-06-29 11:15:46",
                "author": {
                    "uid": 10917,
                    "real_name": "",
                    "status": 1,
                    "avatar": "/static/images/head.jpg",
                    "nickname": "haoku用户",
                    "count_start": 0
                }
            }
        ]
			}).catch(e => {});
		},
		goDetail(item){
			if(item.is_type == 1){
				uni.navigateTo({
					url: '/pages/plantGrass/plant_detail/index?id='+item.community_id
				});
			}else{
				uni.navigateTo({
					//#ifdef APP
					url: '/pages/short_video/appSwiper/index?id='+item.community_id
					//#endif
					//#ifndef APP
					url: '/pages/short_video/nvueSwiper/index?id='+item.community_id
					//#endif
				});
			}
		}
	}
};
</script>

<style scoped lang="scss">
@import '../style/main.scss';
.plant-count{
	background: #ffffff;
	box-shadow: 4rpx 2rpx 12rpx 2rpx rgba(0, 0, 0, 0.03);
}
.plant_bg{
	margin-bottom: 0;
	border-radius: 0;
	padding: 24rpx 20rpx 27rpx 30rpx;
	background-size: 100% 100%;
	background-repeat: no-repeat;
	.more-btn{
		top: 22rpx;
	}
}
.live-wrapper {
	position: relative;
	width: 100%;
	box-sizing: border-box;
	overflow: hidden;
	border-radius: 16rpx;
	padding: 20rpx 20rpx 0;
	image {
		width: 100%;
		height: 400rpx;
	}
	.item{
		position: relative;
		width: 280rpx;
		height: 280rpx;
		display: inline-block;
		overflow: hidden;
		margin-right: 20rpx;
		/deep/image,.easy-loadimage,uni-image {
			width: 280rpx;
			height: 280rpx;
		}
		.img-box16{
			/deep/image,.easy-loadimage,uni-image {
				border-radius: 16rpx;
			}
		}
		.img-box0{
			/deep/image,.easy-loadimage,uni-image {
				border-radius: 0;
			}
		}
		&::before{
			content: "";
			display: block;
			width: 280rpx;
			height: 280rpx;
			background: linear-gradient(180deg, rgba(0, 0, 0, 0) 0%, rgba(0, 0, 0, 0.4) 100%);
			position: absolute;
			top: 0;
			left: 0;
			z-index: 9;
		}
		.item_text{
			width: 260rpx;
			padding: 17rpx 15rpx;
			position: absolute;
			left: 0;
			bottom: 0;
			color: #ffffff;
			z-index: 9;
			image {
				width: 34rpx;
				height: 34rpx;
				border-radius: 100%;
			}
			.text_name{
				font-size: 24rpx;
				width: 260rpx;
				word-break: normal;
				word-wrap: break-word;
				display: block;
			}
			.text_count{
				margin-top: 12rpx;
				align-items: center;
				.name{
					font-size: 18rpx;
					margin-left: 10rpx;
					max-width: 200rpx;
				}
			}
		}
		&.plant-item1{
			width: 324rpx;
			height: 440rpx;
			margin-bottom: 20rpx;
			&:nth-child(2n){
				margin-right: 0;
			}
			/deep/image,.easy-loadimage,uni-image {
				width: 324rpx;
				height: 334rpx;
			}
			&::before{
				width: 324rpx;
				height: 334rpx;
			}
			.item_text{
				position: static;
				color: #282828;
				position: relative;
				width: 100%;
				height: 110rpx;
				.text_name {
					font-size: 28rpx;
					font-weight: bold;
				}
				.text_count{
					width: 100%;
					display: flex;
					align-items: center;
					.name{
						max-width: 160rpx;
					}
				}
				.like_count{
					position: absolute;
					bottom: 10rpx;
					right: 20rpx;
					font-size: 24rpx;
					display: flex;
					align-items: center;
					.iconfont{
						margin-right: 6rpx;
					}
					.icon-shoucang1{
						color: #E93323;
					}
				}
				image {
					width: 34rpx;
					height: 34rpx;
					border-radius: 100%;
				}
			}
		}
	}
}
</style>
