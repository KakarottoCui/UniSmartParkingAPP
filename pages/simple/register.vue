<template>
	<view>
		<view class="warp">
			<view class="name">欢迎注册智能停车场App</view>
			<u-form labelPosition="left" label-width="70" :model="form" ref="uForm">
				<u-form-item prop="nickname" borderBottom label="姓名">
					<u-input v-model="form.nickname" placeholder="请输入姓名"/>
				</u-form-item>
				<u-form-item prop="mobile" borderBottom label="手机号">
					<u-input v-model="form.mobile" placeholder="请输入手机号"/>
				</u-form-item>
				<u-form-item borderBottom label="车牌号" prop="plateNumber">
					<u-input v-model="form.plateNumber" placeholder="请输入车牌号"/>
				</u-form-item>
				<u-form-item label="性别">
					<u-radio-group v-model="form.gender" @change="radioGroupChange">
						<u-radio name="1"></u-radio>男 
						<view style="margin-left: 40rpx;"></view>
						<u-radio name="0"></u-radio>女
					</u-radio-group>
				</u-form-item>
				<u-form-item borderBottom label="停车场">
					<picker @change="parkChange" :value="index" :range="array">
						<view class="uni-input">{{array[index]}}</view>
					</picker>
				</u-form-item>
				<u-form-item label="密码" borderBottom>
					<u-input v-model="form.pass" type="password" placeholder="请输入密码"/>
				</u-form-item>
				<u-form-item label="确认密码" borderBottom>
					<u-input v-model="form.pass2" type="password" placeholder="请确认密码"/>
				</u-form-item>
			</u-form>
			<view class="buttonBox">
				<u-button type="primary" text="注册" @tap="sub()"></u-button>
			</view>
		</view>
		
		<!-- <picker @change="radioGroupChange" :value="index" :range="array">
			<view class="uni-input">{{parkInfo[index].name}}({{parkInfo[index].orgName}})</view>
		</picker> -->
		
	</view>
</template>

<script>
	
	import appRequest from "@/common/appRequestUrl.js"
	import base64 from "@/common/base64.js"
	export default {
		data() {
			return {
				array: [],
				index:0,
				parkInfo:[],
				selItem:{},
				form:{
					mobile:"",
					nickname:"",
					gender:"1",
					plateNumber:"",
					orgId:0,
					orgName:"",
					parkManageId:0,
					parkManageName:0,
					pass:"",
					pass2:""
				},
				show:true
			};
		},
		onLoad(){
			
			this.getParkInfo();
		},
		methods:{
			parkChange(e){
				this.index = e.detail.value;
				let info = this.parkInfo[this.index];
				this.form.orgId = info.orgId;
				this.form.orgName = info.orgName;
				this.form.parkManageId = info.id;
				this.form.parkManageName = info.name;
			},
			radioGroupChange(e){
				this.form.gender = e;
			},
			sub(){
				let _this = this;
				
				if(_this.form.nickname =="" || _this.form.parkManageName == ""){
					uni.showToast({
						title:"请填写完整",
						icon:"none"
					})
					
					return;
				}
				
				if( !_this.$u.test.mobile(_this.form.mobile) ){
					uni.showToast({
						title:"请填写正确的手机号格式",
						icon:"none"
					})
					return;
				}
				
				if( !_this.$u.test.carNo(_this.form.plateNumber) ){
					uni.showToast({
						title:"请填写正确的车牌号格式",
						icon:"none"
					})
					return;
				}
				
				if(_this.form.pass.length<6  || _this.form.pass2.length<6 || _this.form.pass!=_this.form.pass2 ){
					
					uni.showToast({
						title:"请输入两次密码并保持一致，密码至少6位数",
						icon:"none"
					})
					
					return;
				}
				
				let base64en = new base64()
				this.form.pass = base64en.encode(this.form.pass);
				
				appRequest.request({
					method: "POST",
					data: _this.form,
					url: appRequest.urlMap.register,
					success: function(res) {
						uni.showModal({
							title:"信息",
							content:"注册成功，前往登录。",
							showCancel:false,
							success:function(){
								uni.reLaunch({
									url:"/pages/simple/login"
								})
							}
						})
						
						
						//alert(JSON.stringify(res))
					},
					fail: function(res) {
						//alert(JSON.stringify(res))
						_this.$api.msg("请求异常");
						// _this.logining = false;
						// _this.data.pass = "";
					}
				})
				
			},
			changeCar(e){
				this.form.plateNumber = e;
			},
			getParkInfo(){
				let _this = this;
				appRequest.request({
					method: "POST",
					url: appRequest.urlMap.getInfo,
					success: function(res) {
						_this.parkInfo = res.data;
						if(_this.parkInfo && typeof(_this.parkInfo) == 'object'){
							_this.parkInfo.map(function(item,index){
								_this.array.push(item.name+"("+item.orgName+")")
							})
							
							let info = _this.parkInfo[_this.index];
							_this.form.orgId = info.orgId;
							_this.form.orgName = info.orgName;
							_this.form.parkManageId = info.id;
							_this.form.parkManageName = info.name;
							
						}
					},
					fail: function(res) {
						
						uni.showToast({
							title:"网络异常",
							icon:"none"
						})
						
					}
				})
				
			}
		},onReady() {
			this.$refs.uForm.setRules(this.rules);
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
