<template>
	<view class="search-wrapper" :style="{ 'background-color': bgcolor }" @click="handleClick">
		<view class="search-box" :style="{ 'border-radius': radius + 'px' }">
			<uni-icons type="search" size="16"></uni-icons>
			<text class="placehoder">搜索</text>
		</view>
	</view>
</template>

<script>
export default {
	name: 'my-search',
	data() {
		return {};
	},
	props: {
		// 背景颜色
		bgcolor: {
			type: String,
			default: '#C00000'
		},
		// 圆角尺寸
		radius: {
			type: Number,
			// 单位是 px
			default: 18
		}
	},
	methods: {
		handleClick() {
			this.$emit('click');
		}
	}
};
</script>

<style lang="scss">
.search-wrapper {
	text-align: center;
	display: flex;
	// background-color: #c00000;
	justify-content: center;
	align-items: center;
	height: 100rpx;
	.search-box {
		// flex: auto;
		text-align: center;
		display: flex;
		justify-content: center;
		align-items: center;
		background-color: #fff;
		width: 95%;
		height: 80rpx;
		// border-radius: 20rpx;
		font-size: 30rpx;
		font-weight: bold;
		.placehoder {
			margin-left: 6rpx;
		}
	}
}
</style>
