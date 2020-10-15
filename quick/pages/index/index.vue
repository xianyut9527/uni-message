<template>
	<view class="content">
		<view class="text-area">
			<view style="padding:50rpx 0;">
				<view class="uni-padding-wrap uni-common-mt">
				  <view class="list mb40">
						<radio-group @change="radioChange($event,1)">
							<label class="radio mr20" v-for="(item,index) in directionList" :key="index"><radio :value="item.direction" :checked="item.checked" />{{ item.title }}</label>
						</radio-group>  
				  </view>
				  <view class="list mb40">
					<radio-group @change="radioChange($event,2)">
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
				  <view class="list mb100">
				  	  <view>
				  		<switch @change="switchChange($event,'time')" checked/>
				  		时间
				  	  </view>
					  <input v-if="messageData.time" type="number" v-model="timeNum" class="uni-input" placeholder="输入时间" style="border:1px solid #CCCCCC; margin-top:30rpx;padding:10rpx;border-radius:10rpx;"/>
				  </view>
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
				timeNum:3000,
				messageData:{
					type:'default',
					mask:false,
					icon:true,
					respond:false,
					time:true,
					direction:'top'
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
				],
				directionList:[
					{
					 direction:'top',
					 title:'上',
					 checked:true	  
					},
					{
					 direction:'center',
					 title:'中'	 
					},
					{
					 direction:'bottom',
					 title:'下'	 
					}
				]
			}
		},
		onLoad() {

		},
		mounted(){
/* 			setTimeout(()=>{
				this.$refs.message.show({
					msg:'hello world,hello world,hello world,hello world,hello world,hello world,hello world,hello world,hello world,hello world,hello world,hello world,hello world',
				})
			},1000) */
		},
		methods: {
			radioChange: function(evt,index) {
				    if(index===2){
						for (let i = 0; i < this.radioList.length; i++) {
							if (this.radioList[i].value === evt.target.value) {
								this.current = i;
								break;
							}
						}
						this.messageData.type = evt.detail.value;
					}else{
						this.messageData.direction = evt.target.value;
						console.log('---选择方向---',evt.target.value);
					}				
				
			},
			switchChange(e,type){
				let val = e.detail.value;
				console.log(type,val);
				this.messageData[type] = val;
			},
			showMessage(){ //显示message
			    let n = parseInt(100*Math.random());
			    let ranStr = '点击了' + (this.count++) + '下';//this.fromCharCode(n<5?5:n);
				this.$refs.message.show({
					type:this.messageData.type, //默认default
					msg:ranStr, //显示内容
					icon:this.messageData.icon, //显示icon
					mask:this.messageData.mask, //遮罩
					time:this.messageData.time?this.timeNum:false, //无限期时间
				    direction:this.messageData.direction //方向
				})
				let id = this.$refs.message.id; //获取弹窗id,需要在弹出后获取
				if(this.messageData.time===false){
					if(this.messageData.mask){
						this.$refs.message.show({
							type:'warning',
							msg:'注意!! 当前遮罩已开启,不可点击,为了方便测试这里设置了10秒后关闭当前消息,你也可以关闭指定某个消息,详情查看说明文档及demo.',
							time:10000
						}) 
						setTimeout(()=>{
							this.$refs.message.close(id); //关闭某个弹窗
						},10000)
					}
								
				}
				
				
			},
			fromCharCode(num){
				let str = '';
				for(let i=0; i<num; i++){
					let n = parseInt(90*Math.random());
					let nb = n<65?65:n; 
					str += String.fromCharCode(nb);
				}
				return str;
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
