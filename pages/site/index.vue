<template>
	<view class="warp">
		<u-swiper
			:list="list1"
			indicator
			height="368rpx"
			indicatorMode="dot"
			@click="click"
		></u-swiper>
		<view class="u-m-15"></view>
		
		<view class="all_title_box">
			<view class="title">车位情况</view>
		</view>
		<!-- <u-notice-bar :text="text1" speed="40" color="#ff0000" bgColor="#ffffff"></u-notice-bar> -->
		<view class="car_park u-m-t-30" style="width: 100%;" >
			<picker @change="car_confirm" :value="index" :range="columns" style="width: 100%;">
				<view class="uni-input">{{columns[index]}}</view>
			</picker>
			<u-icon name="arrow-down" color="#999" size="16"></u-icon>
		</view>
		<view class="lattice_box" style="width: 100%;">
			
			<view class="park-content">
				<view v-for="(item,index) in selObj.carParkItems" :key="index" class="demo-layout" :class="item.stat == 1?'parking':''">
					<view>{{item.itemName}}</view>
					<view>{{item.stat == 0?'空置':'使用中'}}</view>
				</view>
			</view>
		
		</view>
		
	</view>
</template>

<script>
	
	import appRequest from "@/common/appRequestUrl.js"
	
	export default {
		data() {
			return {
				parkInfo:[],
				index:0,
				selObj:{},
				list1: [
					'/static/coolc/banner_03.jpg'
				],
				text1: '总校区职工停车场出口车道车牌识别相机设备疑似离线，请您尽快处理；',
				car_number: "",
				show: false,
				columns: [
					[]
				],
				table: [{
						time: '刚刚',
						title: '总校公共停车场: 粤M12345 入场'
					}
				]
			}
		},
		onLoad:function(){
			this.getParkInfo();
		},
		methods: {
			click(e) {
				let urls = '';
				if(e == 0) {
					urls = "";
				}
				if(e== 1) {
					urls = "/pages/site/price_details"
				}
				if(e== 2) {
					urls = "/pages/site/car_log"
				}
				
				//跳转页面
				uni.navigateTo({
					url: urls
				});
			},
			car_confirm(e) {
				this.selObj = this.parkInfo[e.detail.value];
			},
			getParkInfo(){
				let _this = this;
				appRequest.request({
					method: "POST",
					url: appRequest.urlMap.getInfo,
					success: function(res) {
						_this.parkInfo = res.data;
						if(_this.parkInfo && typeof(_this.parkInfo) == 'object'){
							_this.columns = [];
							_this.parkInfo.map(function(item,index){
								_this.columns.push(item.name+"("+item.orgName+")")
							})
							_this.selObj = _this.parkInfo[0];
							_this.car_number = _this.columns[0];
						}
					},
					fail: function(res) {
						
						uni.showToast({
							title:"网络异常",
							icon:"none"
						})
						
					}
				})
				
			},checkIndex(){
				let _this = this;
				_this.parkInfo.map(function(item,index){
					if((item.name+"("+item.orgName+")") == _this.car_number){
						_this.selObj = item;
					}
				})
			}
		}
	}
</script>

<style lang="scss">
	.warp {
		width: 100vw;
		padding: 0 30rpx 30rpx 30rpx;
		background: #fff;
	}
	
	.tbody {
		height: 500rpx;
		overflow-y: auto;
		overflow-x: hidden;
	}
	.w1 {
		width: 28%;
		padding-left: 0rpx!important;
		padding-right: 0rpx!important;
	}
	.w2 {
		width: 72%;
		text-align: left;
	}
	.wrap{
		width:100%;
	}
	.demo-layout{
		//box-shadow: 1px 1px 20px #19be6b;
		box-shadow: 1px 1px 15px rgba(0, 0, 0, 0.3);
		height: 80rpx;
		width: 140rpx;
		padding: 20rpx;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		border-radius: 10rpx;
		font-size: 20rpx;
		// color: #19be6b;
		margin: 15rpx 15rpx;
	}
	.parking{
		color: #fa3534;
		box-shadow: 1px 1px 15px rgba(250, 53, 52, 0.6);
	}
	.park-content{
		width: 100%;
		flex-wrap: wrap; 
		display: flex;
		justify-content: flex-start;
		padding: 10rpx;
		margin-top: 30rpx;
	}
</style>
