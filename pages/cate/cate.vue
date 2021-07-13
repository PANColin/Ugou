<template>
	<view class="pyg-cate-index">
		<!-- 分类 -->
		<view class="pyg-cate">
			<!-- 左侧菜单 -->
			<scroll-view class="left-menu " scroll-y :style="{ height: windowHeight + 'px' }">
				<view :class="['left-menu-item', index === currentIndex ? 'active' : '']" v-for="(item, index) in leftMenuList" :key="index" @click="changeMenu(index)">
					<text>{{ item }}</text>
				</view>
			</scroll-view>
			<!-- 右侧内容 -->
			<scroll-view class="right-content" scroll-y :scroll-top="scrollTop" :style="{ height: windowHeight + 'px' }">
				<view class="right-content-item" v-for="(item, index) in rightMenuList" :key="index">
					<view class="goods-tilte">
						<text>{{ item.cat_name }}</text>
					</view>
					<view class="goods-list">
						<navigator v-for="Citem in item.children" :key="Citem.cat_id" :url="'/subpkg/goods_list/goods_list?cid=' + Citem.cat_id">
							<image :src="Citem.cat_icon" mode="widthFix" />
							<view class="good-name">{{ Citem.cat_name }}</view>
						</navigator>
					</view>
				</view>
			</scroll-view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			leftMenuList: [],
			rightMenuList: [],
			// 左侧菜单索引
			currentIndex: 0,
			// 右侧滚动的顶部距离
			scrollTop: 0,
			cateList: [],
			windowHeight: 0
		};
	},
	onLoad(options) {
		this.getCateList();
		// windowHeight	可使用窗口高度
		const { windowHeight } = uni.getSystemInfoSync();
		this.windowHeight = windowHeight;
	},
	methods: {
		async getCateList() {
			// 发起请求
			const { data: res } = await uni.$http.get('/categories');
			// 判断是否获取失败
			if (res.meta.status !== 200) return uni.$showMsg();
			// 转存数据
			this.cateList = res.message;
			this.leftMenuList = this.cateList.map(v => v.cat_name);
			this.rightMenuList = this.cateList[0].children;
		},
		// 左侧点击事件
		changeMenu(i) {
			this.currentIndex = i;
			// 为二级分类列表重新赋值
			this.rightMenuList = this.cateList[i].children;
			// 对与滑动组件不能赋值一样的值否则无效(不会进行滚动条重置)
			// this.scrollTop = 0;
			this.scrollTop = this.scrollTop ? 0 : 1;
		}
	}
};
</script>

<style lang="scss">
.pyg-cate-index {
	.pyg-cate {
		display: flex;
		.left-menu {
			flex: 2;
			// text-align: center;
			.left-menu-item {
				height: 80rpx;
				display: flex;
				justify-content: center;
				align-items: center;
				font-size: 26rpx;
				// padding: 20rpx;
			}
			.active {
				color: $themeColor;
				border-left: 5rpx solid currentColor;
			}
		}
		.right-content {
			flex: 5;
			text-align: center;
			.right-content-item {
				.goods-tilte {
					padding: 40rpx 0;
					border-bottom: 1px solid #eee;
					font-weight: bold;
					//   text-align: center;
				}
				.goods-list {
					display: flex;
					flex-wrap: wrap;
					navigator {
						width: 33.33%;
						text-align: center;
						font-size: 26rpx;
						// border: 1px solid #eee;
						box-shadow: 3px 3px 13px #eee;
						padding: 20rpx 0;
						image {
							width: 50%;
						}
						.good-name {
							white-space: nowrap;
							text-overflow: ellipsis;
							overflow: hidden;
						}
					}
				}
			}
		}
	}
}
</style>
