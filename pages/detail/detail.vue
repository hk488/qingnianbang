<template>
	<view class="detail">
		<view class="title">{{detailArr.title}}</view>
		<view class="info">
			<view class="author">编辑：{{detailArr.author}}</view>
			<view class="time">发布日期：{{detailArr.posttime}}</view>
		</view>
		<view class="content">
			<rich-text :nodes="detailArr.content"></rich-text>
			
		</view>
	</view>
</template>

<script>
	import {parseTime} from '@/utils/tool.js'
	export default {
		data() {
			return {
				options:null,
				detailArr: []
			};
		},
		onLoad(e){
			this.options = e
			this.getDetail()
		},
		methods: {
			getDetail(){
				uni.request({
					url:"https://ku.qingnian8.com/dataApi/news/detail.php",
					data:{
						cid: this.options.cid,
						id: this.options.id,
					},
					success:res=>{
						console.log(res);
						res.data.posttime = parseTime(res.data.posttime)
						
						this.detailArr = res.data
						this.saveHistory()
					}
				})
			},
			saveHistory(){
				// 读取缓存中是否有数组，没则空
				let historyArr = uni.getStorageSync("historyArr") || []
				// 此操作，不需要存储datail中所有的数据，按需存储
				let item={
					id:this.detailArr.id,
					classid:this.detailArr.classid,
					picurl:this.detailArr.picurl,
					title:this.detailArr.title,
					looktime:parseTime(Date.now())
				}
				// 判断缓存中是否有相同的id数组
				let index = historyArr.findIndex(i=>{
					return i.id == this.detailArr.id
				})
				if(index>=0){
					historyArr.splice(index,1)
				}
				console.log(index);
				//追加缓存数组
				historyArr.unshift(item)
				//存入storage中
				uni.setStorageSync("historyArr",historyArr)
			}
		}
	}
</script>

<style lang="scss">
.detail{
	padding: 30px;
	.title{
		font-size: 46rpx;
		color: #333;
	}
	.info{
		background: #f6f6f6;
		padding: 20px 10px;
		font-size: 25rpx;
		color: #666;
		display: flex;
		justify-content: space-between;
		margin: 40rpx 0;
	}
	.content{
		img{
			max-width: 100%;
		}
	}
}
</style>
