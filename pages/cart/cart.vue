<template>
	<view v-if="cart.length" class="wrapper">
		<my-address></my-address>
		<view class="cart-title">
			<uni-icons type="shop" size="20"></uni-icons>
			<text class="cart-title-text">购物车</text>
		</view>
		<view class="goods-wrapper">
			<!-- 商品列表区域 -->
			<!-- uni-swipe-action 是最外层包裹性质的容器 -->
			<uni-swipe-action>
				<block v-for="(goods, i) in cart" :key="i">
					<!-- uni-swipe-action-item 可以为其子节点提供滑动操作的效果。需要通过 options 属性来指定操作按钮的配置信息 -->
					<uni-swipe-action-item :right-options="options" @click="swipeActionClickHandler(goods)">
						<my-good :goods="goods" :show-radio="true" :show-num="true" @radio-change="radioChangeHandler" @num-change="numChangeHandler"></my-good>
					</uni-swipe-action-item>
				</block>
			</uni-swipe-action>
		</view>
		<!-- 底部结算 -->
		<my-settle></my-settle>
	</view>

	<!-- 空白购物车区域 -->
	<view class="empty-cart" v-else>
		<image src="/static/cart_empty@2x.png" class="empty-img"></image>
		<text class="tip-text">空空如也~</text>
	</view>
</template>

<script>
// 按需导入 mapState 这个辅助函数
import { mapState, mapMutations } from 'vuex';
// 导入自己封装的 mixin 模块
import badgeMix from '@/mixins/tabbar-badge.js';
export default {
	// 将 badgeMix 混入到当前的页面中进行使用
	mixins: [badgeMix],
	data() {
		return {
			options: [
				{
					text: '删除', // 显示的文本内容
					style: {
						backgroundColor: '#C00000' // 按钮的背景颜色
					}
				}
			]
		};
	},
	computed: {
		// 将 m_cart 模块中的 cart 数组映射到当前页面中使用
		...mapState('m_cart', ['cart'])
	},
	methods: {
		...mapMutations('m_cart', ['updateStatus', 'updateGoodsCount', 'deleteGoods']),
		radioChangeHandler(e) {
			console.log(e);
			this.updateStatus(e);
		},
		numChangeHandler(e) {
			// console.log(e);
			this.updateGoodsCount(e);
		},
		swipeActionClickHandler(item) {
			console.log(item);
			uni.showModal({
				confirmText: '取消',
				confirmText: '确定',
				content: '请确定要删除么？亲',
				title: '系统提示',
				showCancel: true,
				success: res => {
					if (res.confirm) {
						this.deleteGoods(item);
						uni.$showMsg({ title: '删除成功' });
					} else {
						// console.log(res.cancel);
						uni.$showMsg({ title: '取消删除' });
					}
				}
			});
		}
	}
};
</script>

<style lang="scss">
.wrapper {
	padding-bottom: 50px;
	.cart-title {
		display: flex;
		align-items: center;
		padding: 20rpx;
		.cart-title-text {
			margin-left: 10rpx;
			font-size: 30rpx;
			font-weight: bold;
		}
	}
}
.empty-cart {
	display: flex;
	flex-direction: column;
	align-items: center;
	padding-top: 150px;

	.empty-img {
		width: 90px;
		height: 90px;
	}

	.tip-text {
		font-size: 12px;
		color: gray;
		margin-top: 15px;
	}
}
</style>
