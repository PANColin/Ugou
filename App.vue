<script>
export default {
	onLaunch: function() {
		console.log('App Launch');
	},
	onShow: function() {
		console.log('App Show');
	},
	onHide: function() {
		console.log('App Hide');
	}
};
</script>

<style lang="scss">
/*每个页面公共css */
// $themeColor: #eb4450;
.search-box {
	position: sticky;
	top: 0;
	z-index: 999;
}
</style>
