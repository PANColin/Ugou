<template>
	<view>
		<!-- 轮播图 -->
		<view class="pyg-swiper">
			<swiper indicator-dots autoplay circular>
				<!-- 默认数组的当前项的下标变量名默认为 index，数组当前项的变量名默认为 item 只有一层可以省略不写 -->
				<swiper-item v-for="item in swiperList" :key="item.goods_id">
					<navigator open-type="navigate" :url="'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id">
						<image :src="item.image_src" mode="widthFix" lazy-load="true" />
					</navigator>
				</swiper-item>
			</swiper>
		</view>
		<!-- 导航 -->
		<view class="pyg-nav">
			<navigator v-for="(item, index) in navList" :key="index" :url="item.name == '分类' ? '/pages/cate/cate' : ''" :open-type="item.name == '分类' ? item.open_type : ''">
				<image :src="item.image_src" mode="widthFix" />
			</navigator>
		</view>
		<!-- 楼层 -->
		<view class="pyg-floor">
			<view class="pyg-floor-group" v-for="(item, index) in floorList" :key="index">
				<!-- 标题 -->
				<view class="title"><image :src="item.floor_title.image_src" mode="widthFix" /></view>
				<!-- 内容 -->
				<view class="floor-img-box">
					<!-- 左侧大图片的盒子 -->
					<navigator class="left-img-box" :url="item.product_list[0].url">
						<image :src="item.product_list[0].image_src" :style="{ width: item.product_list[0].image_width + 'rpx' }" mode="widthFix"></image>
					</navigator>
					<!-- 右侧 4 个小图片的盒子 -->
					<view class="right-img-box">
						<navigator class="right-img-item" v-for="(item2, i2) in item.product_list" :key="i2" v-if="i2 !== 0" :url="item2.url">
							<image :src="item2.image_src" mode="widthFix" :style="{ width: item2.image_width + 'rpx' }"></image>
						</navigator>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			swiperList: [],
			navList: [],
			floorList: []
		};
	},
	onLoad(options) {
		this.getList('/home/swiperdata', 'swiperList');
		this.getList('/home/catitems', 'navList');
		this.getList('/home/floordata', 'floorList');
	},
	methods: {
		async getList(url, list, data = {}) {
			const res = await uni.$http.get(url, data);
			// 请求失败
			if (res.data.meta.status !== 200) return uni.$showMsg();
			// 请求成功，为 data 中的数据赋值
			if (list == 'floorList') {
				res.data.message.forEach(el => {
					el.product_list.forEach(el => {
						el.url = '/subpkg/goods_list/goods_list?' + el.navigator_url.split('?')[1];
					});
				});
			}
			this[list] = res.data.message;

			uni.$showMsg({ title: '数据请求成功', icon: 'success' });
		}
	}
};
</script>

<style lang="scss">
.pyg-swiper {
	swiper {
		width: 750rpx;
		height: 340rpx;
		image {
			width: 100%;
		}
	}
}
.pyg-nav {
	display: flex;
	navigator {
		padding: 20rpx;
		// flex: auto;
		flex: 1;
		image {
			width: 100%;
		}
	}
}
.pyg-floor {
	.pyg-floor-group {
		.title {
			padding: 10rpx 0;
			image {
				width: 100%;
				vertical-align: middle;
			}
		}
		.floor-img-box {
			display: flex;
			padding-left: 10rpx;
			.left-img-box {
				flex: 1;
				image {
					vertical-align: middle;
				}
			}
			.right-img-box {
				flex: 2;
				display: flex;
				flex-wrap: wrap;
				justify-content: space-around;
				align-content: space-between;
				.right-img-item {
					overflow: hidden;
					width: 45%;
					height: 45%;
					image {
						vertical-align: middle;
						width: 100% !important;
						height: 100% !important;
					}
				}
			}
		}
	}
}
</style>
