<template>
	代码仅供参考
				<u--input
					placeholder="手机号码"
					v-model="userInfo.username"
					border="none"
				></u--input>
			</u-form-item>
			<u-form-item
				prop="userInfo.mobile"
				borderBottom
				ref="item1"
			>
				<u--input
					placeholder="登录密码"
					v-model="userInfo.password"
					border="none"
					:password="true"
				></u--input>
			</u-form-item>
		</u--form>
		<view class="buttonBox">
			<u-button type="primary" text="立即登录" @tap="login()"></u-button>
			<u-button @tap="register()" :plain="true" style="margin-top: 40rpx;" type="primary" text="注册账号"></u-button>
		</view>
	</view>
</template>

<script>
	
	import appRequest from "@/common/appRequestUrl.js"
	import base64 from "@/common/base64.js"
	export default {
		data() {
			return {
				userInfo: {
					username: '',
					password: '',
					type:"mobile"
				}
			}
		},
		onLoad:function(){
			
			
			
		},
		methods: {
			register(){
				uni.navigateTo({
					url:"/pages/simple/register"
				})
			},
			login(){
				if(!this.userInfo.username||!this.userInfo.password){
					uni.showToast({
						icon: "none",
						title: "请填写完整手机号和密码"
					})
					return;
				}
				
				let base64en = new base64()
				this.userInfo.password = base64en.encode(this.userInfo.password);
				
				let _this = this;
				appRequest.request({
					method: "POST",
					header:{
						'content-type': 'application/x-www-form-urlencoded',
					},
					data: _this.userInfo,
					url: appRequest.urlMap.login,
					success: function(res) {
						if(res.data.code == 0){
							_this.$api.msg('登录成功');
							_this.getUserInfo();
						}else{
							_this.$api.msg(res.data.msg);
							_this.userInfo.password = ""
						}
						
					},
					fail: function(res) {
						_this.$api.msg("请求异常");
						_this.userInfo.password = "";
					}
				})
			},getUserInfo(){
				let _this = this;
				appRequest.request({
					method: "POST",
					data: {
						mobile:_this.userInfo.username,
						pass:_this.userInfo.password
					},
					url: appRequest.urlMap.getAppUser,
					success: function(res) {
						if(res.data.code == 0){
							try {
								uni.setStorageSync('userInfo', res.data.data);
								uni.switchTab({
									url:"/pages/site/index"
								})
							} catch (e) {
								//console.log(JSON.stringify(e));
								uni.clearStorage();
								_this.userInfo.password = ""
							}	
						}else{
							_this.$api.msg(res.data.msg);
							_this.userInfo.password = ""
						}
						
					},
					fail: function(res) {
						_this.$api.msg("请求异常");
						_this.userInfo.password = "";
					}
				})
				
			}
		}
	}
</script>

<style lang="scss">
	.warp {
		width: 750rpx;
		padding: 160rpx 70rpx 30rpx 70rpx;
		background: #fff;
		
		.name {
			width: 100%;
			text-align: center;
			font-size: 38rpx;
			color: #3366FF;
			padding-bottom: 80rpx;
		}
		
		.u-form-item {
			padding-bottom: 50rpx;
		}
		
		.buttonBox {
			width: 100%;
			padding-top: 30rpx;
			
			.u-button {
				width: 100%;
			}
		}
	}
</style>
