<template>
	<!-- 	导致问题的原因：在商品详情数据请求回来之前，data 中 goods_info 的值为 {}，因此初次渲染页面时，会导致 商品价格、商品名称 等闪烁的问题。
	
	解决方案：判断 goods_info.goods_name 属性的值是否存在，从而使用 v-if 指令控制页面的显示与隐藏： -->
	<!-- <view class="good-detail-index" v-cloak> 不起效果-->
	<view class="good-detail-index" v-if="goodsObj.goods_name">
		<!-- 轮播图样式 -->
		<view class="datail-swiper">
			<swiper indicator-dots autoplay circular>
				<swiper-item v-for="item in goodsObj.pics" :key="item.pics_id" @click="handlePrevewImage(item.pics_mid)">
					<image :src="item.pics_mid" mode="widthFix" />
				</swiper-item>
			</swiper>
		</view>
		<view class="goods_price">￥{{ goodsObj.goods_price }}</view>
		<view class="goods_name_row">
			<view class="goods_name">{{ goodsObj.goods_name }}{{ goodsObj.goods_name }}</view>
			<view class="goods_collect" @click="handleCollect">
				<text :class="['iconfont', isCollect ? 'icon-shoucang1' : 'icon-shoucang']"></text>
				<view class="collect_text">收藏</view>
			</view>
		</view>
		<view class="goods_info">
			<view class="goods_info_title">图文详情</view>
			<view class="goods_info_content">
				<!-- 富文本 -->
				<!-- {{goodsObj.goods_introduce}} -->
				<rich-text :nodes="goodsObj.goods_introduce"></rich-text>
			</view>
		</view>
		<view class="goods-nav"><uni-goods-nav :fill="true" :options="options" :buttonGroup="buttonGroup" @click="onClick" @buttonClick="buttonClick" /></view>
	</view>
</template>

<script>
// 从 vuex 中按需导出 mapState 辅助方法
import { mapState, mapMutations, mapGetters } from 'vuex';

export default {
	computed: {
		// 调用 mapState 方法，把 m_cart 模块中的 cart 数组映射到当前页面中，作为计算属性来使用
		// ...mapState('模块的名称', ['要映射的数据名称1', '要映射的数据名称2'])
		...mapState('m_cart', ['cart']),
		...mapGetters('m_cart', ['total'])
	},
	watch: {
		// 定义 total 侦听器，指向一个配置对象
		total: {
			// handler 属性用来定义侦听器的 function 处理函数
			handler(newVal) {
				const findResult = this.options.find(x => x.text === '购物车');
				if (findResult) {
					findResult.info = newVal;
				}
			},
			// immediate 属性用来声明此侦听器，是否在页面初次加载完毕后立即调用
			immediate: true
		}
	},
	data() {
		return {
			goodsObj: {},
			// 商品是否被收藏
			isCollect: false,
			options: [
				{
					icon: 'shop',
					text: '店铺',
					infoBackgroundColor: '#007aff',
					infoColor: 'red'
				},
				{
					icon: 'cart',
					text: '购物车',
					info: this.$store.state.cart
				}
			],
			buttonGroup: [
				{
					text: '加入购物车',
					backgroundColor: '#ff0000',
					color: '#fff'
				},
				{
					text: '立即购买',
					backgroundColor: '#ffa200',
					color: '#fff'
				}
			]
		};
	},
	onLoad(options) {
		// 获取商品 Id
		const goods_id = options.goods_id;
		// 调用请求商品详情数据的方法
		this.getGoodsDetail(goods_id);
	},
	methods: {
		// 把 m_cart 模块中的 addToCart 方法映射到当前页面使用
		...mapMutations('m_cart', ['addToCart']),
		// 定义请求商品详情数据的方法
		async getGoodsDetail(goods_id) {
			const { data: res } = await uni.$http.get('/goods/detail', { goods_id });
			if (res.meta.status !== 200) return uni.$showMsg();
			// 使用字符串的 replace() 方法，为 img 标签添加行内的 style 样式，从而解决图片底部空白间隙的问题 .webp 格式图片在 ios 设备上无法正常显示的问题：
			res.message.goods_introduce = res.message.goods_introduce.replace(/<img /g, '<img style="display:block;" ').replace(/webp/g, 'jpg');
			// 为 data 中的数据赋值
			this.goodsObj = res.message;
		},
		// 点击预览图片
		handlePrevewImage(current) {
			// 1 先构造要预览的图片数组
			// console.log(this.goodsInfo);
			const urls = this.goodsObj.pics.map(v => v.pics_mid);
			// console.log(urls);
			// 2 接收传递过来的图片url
			wx.previewImage({
				current,
				urls,
				showmenu: true
			});
		},
		// 左侧按钮的点击事件处理函数
		onClick(e) {
			if (e.content.text === '购物车') {
				// 切换到购物车页面
				uni.switchTab({
					url: '/pages/cart/cart'
				});
			}
		},
		// 右侧按钮的点击事件处理函数
		buttonClick(e) {
			// 1. 判断是否点击了 加入购物车 按钮
			if (e.content.text === '加入购物车') {
				// 2. 组织一个商品的信息对象
				const goods = {
					goods_id: this.goodsObj.goods_id, // 商品的Id
					goods_name: this.goodsObj.goods_name, // 商品的名称
					goods_price: this.goodsObj.goods_price, // 商品的价格
					goods_count: 1, // 商品的数量
					goods_small_logo: this.goodsObj.goods_small_logo, // 商品的图片
					goods_state: true // 商品的勾选状态
				};

				// 3. 通过 this 调用映射过来的 addToCart 方法，把商品信息对象存储到购物车中
				this.addToCart(goods);
			}
		}
	}
};
</script>

<style lang="scss">
// 解决闪烁的一种方法也可以 用v-if来解决
// 不起效果
// [v-cloak] {
// 	display: none;
// }
.good-detail-index {
	padding-bottom: 50px;
	// 商品导航组件
	.goods-nav {
		position: fixed;
		bottom: 0;
		width: 100%;
	}
	padding: 90rpx 0;
	.datail-swiper {
		swiper {
			height: 65vw;
			text-align: center;
			image {
				width: 80%;
			}
		}
	}
	.goods_price {
		padding: 20rpx;
		font-size: 32rpx;
		color: $themeColor;
	}
	.goods_name_row {
		padding: 10rpx 0;
		display: flex;
		.goods_name {
			flex: 5;
			padding: 0 10rpx;
			display: -webkit-box;
			overflow: hidden;
			-webkit-box-orient: vertical;
			-webkit-line-clamp: 2;
		}
		.goods_collect {
			flex: 1;
			display: flex;
			flex-direction: column;
			justify-content: center;
			align-items: center;
			border-left: 1rpx solid #000;
			.iconfont {
			}
			.icon-shoucang1 {
				color: orangered;
			}
			.collect_text {
			}
		}
	}

	.goods_info {
		.goods_info_title {
			font-size: 32rpx;
			color: $themeColor;
			font-weight: 600;
			padding: 20rpx;
		}
		.goods_info_content {
		}
	}
}
</style>
