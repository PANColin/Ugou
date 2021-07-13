<template>
	<view class="goods-wrapper">
		<navigator class="goods_item" :url="'/subpkg/goods_detail/goods_detail?goods_id=' + goods.goods_id">
			<!-- 左侧 图片容器 -->
			<view class="goods_img_wrap">
				<image mode="widthFix" :src="goods.goods_small_logo ? goods.goods_small_logo : 'https://ww1.sinaimg.cn/large/007rAy9hgy1g24by9t530j30i20i2glm.jpg'"></image>
			</view>
			<!-- 右侧 商品容器 -->
			<view class="goods_info_wrap">
				<view class="goods_name">{{ goods.goods_name }}</view>
				<view class="goods_price">￥{{ goods.goods_price | tofixed }}</view>
			</view>
		</navigator>
	</view>
</template>

<script>
export default {
	name: 'my-good',
	// 定义 props 属性，用来接收外界传递到当前组件的数据
	props: {
		// 商品的信息对象
		goods: {
			type: Object,
			defaul: {}
		}
	},
	data() {
		return {};
	},
	filters: {
		// 把数字处理为带两位小数点的数字
		tofixed(num) {
			return Number(num).toFixed(2);
		}
	}
};
</script>

<style lang="scss">
.goods-wrapper {
	.goods_item {
		display: flex;
		border-bottom: 1px solid #ccc;
		.goods_img_wrap {
			flex: 2;
			display: flex;
			justify-content: center;
			align-items: center;
			image {
				width: 70%;
			}
		}
		.goods_info_wrap {
			flex: 3;
			display: flex;
			flex-direction: column;
			justify-content: space-around;
			.goods_name {
				display: -webkit-box;
				overflow: hidden;
				-webkit-box-orient: vertical;
				-webkit-line-clamp: 2;
			}
			.goods_price {
				color: $themeColor;
				font-size: 32rpx;
			}
		}
	}
}
</style>
