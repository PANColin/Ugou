<template>
	<view>
		<view class="search-box">
			<!-- 设置圆角 -->
			<uni-search-bar :radius="100" @input="input" cancelButton="none"></uni-search-bar>
		</view>
		<!-- 搜索建议 -->
		<view class="search-sugg" v-if="searchList.length">
			<navigator :url="'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id" class="search-sugg-item" v-for="(item, index) in searchList" :key="item.goods_id">
				<text class="search-sugg-text">{{ item.goods_name }}</text>
				<uni-icons type="arrowright" size="25"></uni-icons>
			</navigator>
		</view>
		<!-- 搜索历史 -->
		<view class="history-wrapper" v-else>
			<view class="history-title">
				<text>搜索历史</text>
				<uni-icons type="trash" size="20" color="#f00" @click="clearHistory"></uni-icons>
			</view>
			<view class="history-item">
				<navigator :url= "'/subpkg/goods_list/goods_list?query=' + item" class="history-item-text" v-for="(item, index) in historys" :key="index">{{ item }}</navigator>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			// 防抖
			timer: null,
			kw: '',
			searchList: [],
			historyList: []
		};
	},
	onLoad() {
		//如果缓存中存在历史记录则复制到data数据中
		this.historyList = JSON.parse(uni.getStorageSync('kw') || '[]');
	},
	computed: {
		historys() {
			// 注意：由于数组是引用类型，所以不要直接基于原数组调用 reverse 方法，以免修改原数组中元素的顺序
			// 而是应该新建一个内存无关的数组，再进行 reverse 反转
			return [...this.historyList].reverse();
		}
	},
	methods: {
		input(e) {
			// 防抖
			clearTimeout(this.timer);

			this.timer = setTimeout(() => {
				// console.log(e.value);
				this.kw = e.value;
				this.getSearchList();
			}, 500);
		},
		async getSearchList() {
			if (this.kw.trim()) {
				// 把data重新名为data
				const { data: res } = await uni.$http.get('/goods/qsearch', { query: this.kw });
				// 判断是否获取失败
				if (res.meta.status !== 200) return uni.$showMsg();
				// console.log(res);
				this.searchList = res.message;
				// 保存历史记录
				this.saveSearchHistory();
			} else {
				this.searchList = [];
				return uni.$showMsg({ title: '输入内容不能为空' });
			}
		},
		// 保存搜索关键词为历史记录
		saveSearchHistory() {
			const set = new Set(this.historyList);
			// 如果存在就先删除
			set.delete(this.kw);
			// 在添加就把存在的提到第一位了
			set.add(this.kw);
			this.historyList = Array.from(set);
			// 调用 uni.setStorageSync(key, value) 将搜索历史记录持久化存储到本地
			uni.setStorageSync('kw', JSON.stringify(this.historyList));
		},
		// 清空历史记录
		clearHistory() {
			uni.showModal({
				confirmText: '取消',
				confirmText: '确定',
				content: '请确定要清除历史记录么？亲',
				title: '系统提示',
				showCancel: true,
				success: res => {
					if (res.confirm) {
						this.historyList = [];
						// 存入缓存中
						// 调用 uni.setStorageSync(key, value) 将搜索历史记录持久化存储到本地
						uni.setStorageSync('kw', '[]');
						uni.$showMsg({ title: '清除历史记录成功' });
					} else {
						// console.log(res.cancel);
						uni.$showMsg({ title: '取消清除历史记录' });
					}
				},
				fail: err => {
					console.log(err);
				}
			});
		}
	}
};
</script>

<style lang="scss">
.search-sugg {
	.search-sugg-item {
		display: flex;
		justify-content: center;
		align-items: center;
		border-bottom: 1px solid #999;
		padding: 10rpx 30rpx;
		font-size: 28rpx;
		font-weight: bold;
		.search-sugg-text {
			flex: auto;
			white-space: nowrap;
			text-overflow: ellipsis;
			overflow: hidden;
		}
		uni-icons {
			margin: 0;
			// flex: 1;
			text-align: center;
		}
	}
}
// 搜索历史
.history-wrapper {
	.history-title {
		display: flex;
		justify-content: space-between;
		padding: 20rpx 20rpx;
		align-items: center;
		border-bottom: 1px solid #999;
	}
	.history-item {
		margin: 20rpx;
		display: flex;
		.history-item-text {
			// border: 1px solid #999;
			margin: 0 20rpx;
			padding: 6rpx;
			background-color: $uni-bg-color-grey;
		}
	}
}
</style>
