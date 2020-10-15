<template>
  <view class="quick-message">
	<view class="quick-message-mask" v-for="(item,index) in msgList" :key="index" v-show="item.mask"></view>
	<view class="quick-message-centre" :style="centerStyle">
	   <view class="safe-height-box" :style="{height:safeHeight + 'px'}"></view>	
       <view class="message-list" v-for="(item,index) in msgList" :class="['msg-list-' + index,item.type + '-message',item.class]">
		   <icon class="msg-icon" :type="item.icon" size="16" v-if="item.icon"/>
		   <text class="msg-text">{{item.msg}}</text>
	   </view>
	</view>
	
  </view>
</template>

<script>
	export default {
		data() {
			return {	
			  msgList:[], //消息列表
			  centerStyle:{},
			  color:{
				  success:'#67c23a',
				  default:'#1989fa',
				  warning:'#e6a23c',
				  error:'#f56c6c'
			  },
			  icon:{
				 success:'success',
				 default:'info',
				 warning:'warn',
				 error:'clear' 
			  },
			  safeHeight:0,
			}
		},
	    mounted(){
		 let that = this;	
		 uni.getSystemInfo({
		        success: function(e) {
					that.safeHeight = e.safeArea.top + 'px';
				}
		 })
				
		},
		methods:{
          show(options){ 
			/**
			 * 参数配置
			 */
			console.log(JSON.stringify(options),'--fx--');
			options.icon?options.icon = this.icon[options.type]:'';
			options.class = 'topShow';
			this.msgList.push(options);
			this.closeMessage(options);
		  },
		  getClass(className){
			 const query = uni.createSelectorQuery().in(this);
			 return new Promise((resolve, rej) => {
				 query.select('.' + className).boundingClientRect(data => {
					resolve(data);
				 }).exec();  
		    })
		  },
		  closeMessage(options){ 
            setTimeout(async ()=>{
				let elHeight = [];
				let messageList = await this.getClass('msg-list-0');
				let centerHeight = -(messageList.height + 10) + 'px';
				this.centerStyle = {
					transform:'translate(-50%,'+centerHeight+')'
				}
				this.msgList.map((item)=>{
					item.class = '';
					return item;
				})
				this.msgList.push();
				setTimeout(()=>{
					console.log('---还原---');
					this.msgList.shift();
					this.centerStyle = {
						transform:'translate(-50%,0)'
					}
				},300)
				
			},options.time)
			  
		  },
		  closeAni(firstHeight){
			  this.msgList.map((item,index)=>{
			  	let yVal = -(firstHeight) + 'px';
			  	this.centerStyle = {
			  		transform:'translateY('+yVal+')',
			  		opacity:!index?0:1
			  	}
			  	return item;
			  })
			  this.msgList.push();
		  }
		  
		}
	}
</script>

<style lang="scss">
  .quick-message{
	background-color:transparent;
    .quick-message-mask{
		width:100%;
		height:100vh;
		position:fixed;
		top:0;
		left:0;
		z-index:9999998;
		background-color:transparent;
	}
	.quick-message-centre{
		position:absolute;
		top:10rpx;
		left:50%;
		transform:translate(-50%,0);
		transition: all .3s;
	}
	.message-list{
		box-shadow:0 2px 8px #CCCCCC;
		padding:10rpx 20rpx;
		border-radius:8rpx;
		font-size:26rpx;
		display:flex;
		flex-direction:row;
		align-items:center;
		margin-bottom:10px;
		.msg-icon{
			margin-right:10rpx;
		}
	}
	/** 颜色主题 **/
	.default-message{
		background-color:#FFFFFF;
	}
	.success-message{
		background-color:#f0f9eb;
	}
	.warning-message{
		background-color:#fdf6ec;
	}
	.error-message{
		background-color:#fef0f0;
	}
	/** 动画相关 **/
	.topShow{
		animation: topBottom .3s;
	}
	@keyframes topBottom
	{
	0% {
		opacity:0;
		transform: translate(0, -60rpx);
	   }
	100% {
		opacity:1;
		transform: translate(0, 0);
		}
	}
	
  }
</style>