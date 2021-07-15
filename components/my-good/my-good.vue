<template>
	<view class="wrapper">
		<view class="left"><radio :checked="goods.goods_state" color="#C00000" v-if="showRadio" @click="clickHandler"></radio></view>
		<view class="goods-wrapper">
			<view @click="test">
				<view class="goods_item">
					<!-- 左侧 图片容器 -->
					<view class="goods_img_wrap">
						<image mode="widthFix" :src="goods.goods_small_logo ? goods.goods_small_logo : 'https://ww1.sinaimg.cn/large/007rAy9hgy1g24by9t530j30i20i2glm.jpg'"></image>
					</view>
					<!-- 右侧 商品容器 -->
					<view class="goods_info_wrap">
						<view class="goods_name">{{ goods.goods_name }}</view>
						<text class="goods_price">￥{{ goods.goods_price | tofixed }}</text>
					</view>
				</view>
			</view>
			<view class="goods_info-bottom" v-if="showNum">
				<!-- <text class="goods_price">￥{{ goods.goods_price | tofixed }}</text> -->
				<uni-number-box :min="1" :value="goods.goods_count" @change="bindChange"></uni-number-box>
			</view>
		</view>
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
		},
		// 是否展示图片左侧的 radio
		showRadio: {
			type: Boolean,
			// 如果外界没有指定 show-radio 属性的值，则默认不展示 radio 组件
			default: false
		},
		// 是否展示价格右侧的 NumberBox 组件
		showNum: {
			type: Boolean,
			default: false
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
	},
	methods: {
		test(e) {
			console.log(e);
			uni.navigateTo({
				url: '/subpkg/goods_detail/goods_detail?goods_id=' + this.goods.goods_id
			});
		},
		// 点击单选框
		clickHandler(e) {
			// console.log(e);
			this.$emit('radio-change', { goods_id: this.goods.goods_id, goods_state: !this.goods.goods_state });
		},
		// num盒子改变事件
		bindChange(val) {
			// console.log(val);
			this.$emit('num-change', {
				// 商品的 Id
				goods_id: this.goods.goods_id,
				// 商品的最新数量 转换成数字类型
				goods_count: +val
			});
		}
	}
};
</script>

<style lang="scss">
.wrapper {
	border-bottom: 1px solid #ccc;
	padding: 20rpx;
	display: flex;
	.left {
		display: flex;
		align-items: center;
	}
	.goods-wrapper {
		display: flex;
		flex-direction: column;
		.goods_info-bottom {
			display: flex;
			justify-content: flex-start;
			// flex-direction: column;
			align-items: center;
			margin: 0 20rpx;
			// .goods_price {
			// 	width: 200rpx;
			// 	color: $themeColor;
			// 	font-size: 32rpx;
			// }
		}
		.goods_item {
			// width: 750rpx;
			display: flex;
			align-items: center;
			.goods_img_wrap {
				// flex: 2;
				padding-left: 20rpx;
				width: 100px;
				display: flex;
				justify-content: center;
				align-items: center;
				image {
					width: 80px;
					height: 80px;
				}
			}
			.goods_info_wrap {
				// flex: 1;
				padding-left: 20rpx;
				display: flex;
				flex-direction: column;
				justify-content: space-around;
				.goods_name {
					display: -webkit-box;
					overflow: hidden;
					-webkit-box-orient: vertical;
					-webkit-line-clamp: 2;
					font-size: 30rpx;
					font-weight: bold;
					padding-right: 40rpx;
				}
				.goods_price {
					width: 200rpx;
					color: $themeColor;
					font-size: 32rpx;
				}
			}
		}
	}
}
</style>
