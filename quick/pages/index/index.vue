<template>
	<view class="content">
		<view class="text-area">
			<view style="padding:50rpx 0;">
				<view class="uni-padding-wrap uni-common-mt">
				  <view class="list mb40">
					<radio-group @change="radioChange">
						<label class="radio mr20" v-for="(item,index) in radioList" :key="index"><radio :value="item.type" :checked="item.checked" />{{ item.title }}</label>
					</radio-group>  
				  </view>	
                  <view class="list mb40">
					  <view>
					    <switch @change="switchChange($event,'icon')" checked/>
						 显示icon
					  </view>
				  </view>
				  <view class="list mb40">
				  	  <view>
				  		<switch @change="switchChange($event,'mask')" />
						遮罩
				  	  </view>
				  </view>
<!-- 				  <view class="list mb100">
				  	  <view>
				  		<switch @change="switchChange($event,'respond')" />
				  		等待响应
				  	  </view>
				  </view> -->
				  <button type="primary" @click="showMessage">触发</button>
				</view>
			</view>
		</view>
		<!-- 全局message组件 -->
		<quick-message ref="message"></quick-message>
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: 'Hello',
				count:0,
				current:0,
				messageData:{
					type:'default',
					mask:false,
					icon:true,
					respond:false //响应状态为true强制开启遮罩
				},
				radioList:[
					{
						title:'普通',
						type:'default',
						checked:'true'
					},
					{
						type:'success',
						title:'成功'
					},
					{
						type:'warning',
						title:'警告'
					},
					{
						type:'error',
						title:'错误'
					}					
				]
			}
		},
		onLoad() {

		},
		methods: {
			radioChange: function(evt) {
						for (let i = 0; i < this.radioList.length; i++) {
							if (this.radioList[i].value === evt.target.value) {
								this.current = i;
								break;
							}
						}	
							
				this.messageData.type = evt.detail.value;
			},
			switchChange(e,type){
				let val = e.detail.value;
				console.log(type,val);
				this.messageData[type] = val;
			},
			showMessage(){ //显示message
				this.$refs.message.show({
					type:this.messageData.type, //默认default
					msg:!this.messageData.respond?('点击了 ' + this.count ++ + ' 下'):'加载中...', //显示内容
					icon:this.messageData.icon, //显示icon
					mask:this.messageData.mask, //遮罩
					respond:this.messageData.respond //respond状态
				})
/* 				let id = this.$refs.message.id; //获取弹窗id,需要在弹出后获取
				if(this.messageData.respond){
					setTimeout(()=>{
						this.$refs.message.close(id); //关闭某个弹窗
					},5000)				
				} */
				
				
			}
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
	.mb40{
		margin-bottom:40rpx;
	}
	.mb100{
		margin-bottom:100rpx;
	}
	.mr20{
		margin-right:20rpx;
	}
</style>
