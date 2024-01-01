<template>
	<view>
		<view class="warp_user">
			<view class="top_user_box">
				<image src="/static/coolc/park.png"></image>
				<view class="user_info">
					<view class="username">{{userInfo.nickname}}</view>
					<view class="mobile">{{userInfo.mobile}}</view>
				</view>
			</view>

			<view class="icon_boxs">
				<navigator class="i" hover-class="none" @click="showCarInfo">
					<view class="icon_box">
						<u-icon name="grid-fill" color="#fff" size="32"></u-icon>
					</view>
					<text class="n">车辆信息</text>
				</navigator>
				<navigator class="i" hover-class="none" @click="showUserInfo">
					<view class="icon_box">
						<u-icon name="email-fill" color="#fff" size="32"></u-icon>
					</view>
					<text class="n">个人信息</text>
				</navigator>
			</view>
		</view>
		
		<u-cell-group :border="false">
			<u-cell icon="setting" style="margin: 10rpx 0;" @click="info" :isLink="true" title="应用信息"></u-cell>
			<u-cell @click="jump" icon="bell" style="margin: 10rpx 0;" :isLink="true" :title="login?'退出登录':'前往登录'"></u-cell>
		</u-cell-group>
	</view>
</template>

<script>
	import appRequest from "@/common/appRequestUrl.js"
	export default {
		data() {
			return {
				userInfo:{
					nickname:"",
					mobile:""
				},
				login:false
			}
		},
		onShow(){
			let info = appRequest.getUserInfo();
			//console.log(JSON.stringify(this.userInfo))
			if(!info){
				uni.clearStorage()
				this.userInfo.nickname = "游客"
				this.userInfo.mobile = "登录后查看"
			}else{
				this.userInfo = info;
				this.login = true;
			}
		},
		methods: {
			navi(url) {
				uni.navigateTo({
					url: url
				});
			},jump(){
				if(this.login){
					this.login = false;
					uni.clearStorage();
					uni.switchTab({
						url:"/pages/site/index"
					})
				}else{
					uni.clearStorage();
					uni.navigateTo({
						url:"/pages/simple/login"
					})
				}
			},info(){
				uni.showModal({
					title:"应用信息",
					content:"智能停车场App，版本0.808",
					showCancel:false
				})
			},showCarInfo(){
				if(this.login){
					uni.showModal({
						title:"车辆信息",
						content:"当前绑定车牌号："+this.userInfo.plateNumber,
						showCancel:false
					})
				}else{
					uni.showToast({
						title:"请登录后操作",
						icon:"none"
					})
				}
			},showUserInfo(){
				if(this.login){
					uni.navigateTo({
						url:"/pages/user/userInfo"
					})
				}else{
					uni.showToast({
						title:"请登录后操作",
						icon:"none"
					})
				}
				
			}
		}
	}
</script>

