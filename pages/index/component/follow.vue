<template>
	<view v-if='!subscribe'>
		<!-- #ifdef H5 -->
		<view class="follow acea-row row-between-wrapper" :style="'background:' + bgColor + ';margin-top:' + mbConfig + 'rpx;margin-left:'+prConfig+'rpx;margin-right:'+prConfig+'rpx;'">
			<view class="picTxt acea-row row-middle">
				<view class="pictrue"><image :src="imgConfig"></image></view>
				<view class="name line1">{{ titleConfig }}</view>
			</view>
			<view class="notes acea-row row-center-wrapper" :style="'color:' + themeColor + ';border-color:' + themeColor + ';'" @click="followTap">关注</view>
		</view>
		<view class="followCode" v-if="followCode">
			<view class="pictrue">
				<view class="code-bg" :style="{ 'background-image': `url(${domain}/static/images/code-bg.png)`}"><img class="imgs" :src="followUrl" /></view>
			</view>
			<view class="mask" @click="closeFollowCode"></view>
		</view>
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
// #ifdef H5
import { getSubscribe } from '@/api/public';
import { HTTP_REQUEST_URL } from '@/config/app';
export default {
	name: 'follow',
	props: {
		dataConfig: {
			type: Object,
			default: () => {}
		}
	},
	data() {
		return {
			domain: HTTP_REQUEST_URL,
			followCode: false,
			followUrl:this.dataConfig.codeConfig.url,
			bgColor:this.dataConfig.bgColor.color[0].item,
			imgConfig:this.dataConfig.imgConfig.url,
			mbConfig:this.dataConfig.mbConfig.val*2,
			prConfig:this.dataConfig.prConfig&&this.dataConfig.prConfig.val*2 || 0,
			themeColor:this.dataConfig.themeColor.color[0].item,
			titleConfig:this.dataConfig.titleConfig.value,	
			subscribe: true
		};
	},
	created() {},
	mounted() {
		getSubscribe().then(res => {
			this.subscribe = res.data.subscribe;
		}).catch(() => {})
	},
	methods: {
		followTap(){
			this.followCode = true;
		},
		closeFollowCode(){
			this.followCode = false
		},
	}
}
// #endif
</script>

<style lang="scss" scoped>
.follow {
	padding: 0 20rpx;
	height: 140rpx;
	background: rgba(0, 0, 0, 0.02);
	.picTxt {
		.pictrue {
			width: 92rpx;
			height: 92rpx;
			border-radius: 50%;
			image {
				width: 100%;
				height: 100%;
				border-radius: 50%;
			}
		}
		.name {
			font-size: 32rpx;
			color: #000;
			margin-left: 32rpx;
			width: 400rpx;
		}
	}
	.notes {
		font-size: 28rpx;
		color: #02a0e8;
		width: 120rpx;
		height: 52rpx;
		border: 2rpx solid rgba(2, 160, 232, 1);
		opacity: 1;
		border-radius: 6rpx;
	}
}
.followCode {
	.pictrue {
		width: 500rpx;
		height: 720rpx;
		border-radius: 12px;
		left: 50%;
		top: 50%;
		margin-left: -250rpx;
		margin-top: -360rpx;
		position: fixed;
		z-index: 10000;
		.code-bg {
			display: flex;
			justify-content: center;
			width: 100%;
			height: 100%;
			background-size: 100% 100%;
		}
		.imgs {
			width: 310rpx;
			height: 310rpx;
			margin-top: 92rpx;
		}
	}
	.mask {
		z-index: 9999;
	}
}
</style>
