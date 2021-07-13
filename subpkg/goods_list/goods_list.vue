<template>
	<view class="good-list-index">
		<view class="search-box">
			<!-- 自定义搜索组件 -->
			<!-- <my-search :bgcolor="'yellow'" :radius="30"></my-search> -->
			<my-search @click="goSearch"></my-search>
		</view>
		<!-- block包裹渲染的时候不会生成任何标签 占位作用 -->
		<block v-for="item in goodsList" :key="item.goods_id"><my-good :goods="item"></my-good></block>
	</view>
</template>

<script>
export default {
	data() {
		return {
			// 请求参数对象
			queryObj: {
				// 查询关键词
				query: '',
				// 商品分类Id
				cid: '',
				// 页码值
				pagenum: 1,
				// 每页显示多少条数据
				pagesize: 10
			},
			goodsList: [],
			total: 0,
			// 是否正在请求数据
			isloading: false,
			// 总页数
			totalPages: 0
		};
	},
	// 触底的事件
	/**
	 * 页面上拉触底事件的处理函数
	 */
	onReachBottom() {
		// 判断数据是否加载完毕
		if (this.queryObj.pagenum >= this.totalPages) return uni.$showMsg({ title: '没有更多数据了' });
		// 判断是否正在请求其它数据，如果是，则不发起额外的请求 //请求数据加了mask:true,效果一样的基本，这里学习一下思路
		if (this.isloading) return;
		// 让页码值自增 +1
		this.queryObj.pagenum += 1;
		// 重新获取列表数据
		this.getGoodsList();
	},
	// 下拉刷新事件
	/**
	 * 页面相关事件处理函数--监听用户下拉动作
	 */
	onPullDownRefresh() {
		// 1. 重置关键数据
		this.queryObj.pagenum = 1;
		this.total = 0;
		this.isloading = false;
		this.goodsList = [];

		// 2. 重新发起请求
		this.getGoodsList(() => uni.stopPullDownRefresh());
	},
	onLoad(options) {
		// 将页面参数转存到 this.queryObj 对象中
		this.queryObj.query = options.query || '';
		this.queryObj.cid = options.cid || '';
		this.getGoodsList();
	},
	methods: {
		// 获取商品列表数据的方法
		async getGoodsList(cb) {
			// ** 打开节流阀
			this.isloading = true;
			// 发起请求
			const { data: res } = await uni.$http.get('/goods/search', this.queryObj);
			if (res.meta.status !== 200) return uni.$showMsg();
			// 只要数据请求完毕，就立即按需调用 cb 回调函数
			cb && cb();
			// 总条数
			const total = res.message.total;
			this.total = total;
			// 计算总页数
			this.totalPages = Math.ceil(total / this.queryObj.pagesize);
			// ** 关闭节流阀
			this.isloading = false;
			// console.log(res);
			// 为数据赋值：通过展开运算符的形式，进行新旧数据的拼接
			this.goodsList = [...this.goodsList, ...res.message.goods];
		}
	}
};
</script>

<style lang="scss"></style>
