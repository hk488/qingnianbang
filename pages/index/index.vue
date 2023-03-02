<template>
	<view class="home">
			<scroll-view scroll-x class="navscrool">
				<view class="item" :class="index==navIndex ? 'active' : ''"  v-for="(item,index) in navArr" 
				@click="clickNav(index,item.id)">
				{{item.classname}}</view>
				
			</scroll-view>
			
			<view class="content">
				<view class="row" v-for="item in newsArr" :key="item.id">
					<newsbox :item="item" @click.native="goDetail(item)"></newsbox>
				</view>
			</view>
			<view class="loading">
				<view v-if="loading == 1">数据加载中</view>
			    <view v-if="loading == 2">没有更多了...</view>
			</view>
			
	</view>
</template>

<script>
	export default {
		data() {
			return {
				navIndex: 0,
				navArr:[],
				newsArr:[],
				currentPage:1,
				loading:0 //0默认  1加载中  2没有更多了
			}
		},
		onLoad() {
			this.getNavData()
			this.getNewsData()
		},
		onReachBottom() {
			if(this.loading == 2){
				return
			}
			this.currentPage++
			this.loading=1
			this.getNewsData()
		},
		methods: {
			clickNav(index,id){
				this.navIndex=index
				this.currentPage=1
				this.loading = 0
				this.newsArr=[]
				this.getNewsData(id)
			},
			goDetail(item){
				uni.navigateTo({
					url:`/pages/detail/detail?cid=${item.classid}&id=${item.id}`
				})
				console.log(item);
			},
			getNavData(){
				uni.request({
					url:"https://ku.qingnian8.com/dataApi/news/navlist.php",
					success:res=>{
						this.navArr = res.data
					}
				})
			},
			//获取新闻列表 点击传参对应id并接收对应id新闻
			getNewsData(id=50){
				uni.request({
					url:"https://ku.qingnian8.com/dataApi/news/newslist.php",
					data:{
						cid:id,
						page:this.currentPage
					},
					success:res=>{
						if(res.data.length == 0){
							this.loading = 2
						}
						this.newsArr = [...this.newsArr, ...res.data]
					}
				})
			}
		}
	}
</script>

<style lang="less" scoped>		
	.navscrool{
		height: 100rpx;
		background: #f7f8fa;
		white-space: nowrap;
		position: fixed;
		top: var(--window-top);
		left: 0;
		z-index: 10;
		/deep/ ::-webkit-scrollbar {
			width: 4px !important;
			height: 4px !important;
			overflow: auto !important;
			background: transparent !important;
			-webkit-appearance: auto !important;
			display: block;
		}
		.item{
			font-size: 40rpx;
			display: inline-block;
			line-height: 100rpx;
			padding: 0 30rpx;
			color: #333;
			&.active{
			color: #31c27c;
		    }			
		}
		
	}
	
	.content{
		padding: 30rpx;
		padding-top: 130rpx;
		.row{
			border-bottom: 1px dotted #ededed;
			padding: 15rpx 0;
		}
	}
</style>
