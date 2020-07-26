<template>
  <view class="quick-message">
	<view class="quick-message-mask" v-for="(item,index) in msgList" :key="index" v-show="item.mask"></view>
	<view class="quick-message-centre" :style="messageContentStyle">
		<view class="quick-message-list" :class="['classList' + (index+1)]" v-for="(item,index) in msgList" :key="index">
		  <view class="quick-message-list-child" :class="[item.type + '-message',item.class?item.class:'']">
			   <view class="msg-child-content">
				 <icon class="msg-icon" :type="item.icon" :size="item.iconSize?item.iconSize:16" :color="item.iconColor?item.iconColor:''" v-if="item.icon"/>
				 <text class="msg-text" :style="{fontSize:item.textSize + 'rpx'}">{{item.msg}}</text>  
			   </view>

		  </view>
	    </view>
	</view>
	
  </view>
</template>

<script>
	export default {
		data() {
			return {	
			  msgList:[], //消息列表
			  time:3000, //默认message时长
			  showCount:0, //显示message计数
			  closeCount:0,
			  messageContentStyle:null,
			  iconColor:{
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
			  }
			}
		},	
		methods:{
          show(options){ 
			options.type = options.type?options.type:'default';
			options.class = 'show-message' //show
			options.icon = options.icon===false?'':options.icon!==true&&options.icon?options.icon:this.icon[options.type];
			options.iconColor = options.iconColor?options.iconColor:this.iconColor[options.type];
			options.textSize = options.textSize?options.textSize:28;
			this.msgList.push(options);
			this.closeMessage(options.time);
		  },
		  getClass(className){
			return new Promise((resolve, rej) => {
			 uni.createSelectorQuery().select('.' + className).fields({
			               size: true
			           }, (data) => {
			              resolve(data);
			           }).exec();  
		    })
		  },
		  closeMessage(time){  
			setTimeout(async ()=>{	
			  let msgListLength = this.msgList.length-1;
			  let msgListStr = JSON.stringify(this.msgList);
			  this.msgList = this.msgList.map((item,index)=>{
				  index<=this.showCount?item.class = 'close-message':'';
				  return item;
			  })
			  this.msgList.push();
			  this.showCount ++;
			  let countBs = this.showCount?this.showCount:1;
			  let messageList = await this.getClass("classList" + this.showCount);
			  let yNum = -Number((messageList.height * countBs).toFixed(2));
			  this.messageContentStyle = {
			  		transform: 'translate(-50%, '+yNum+'px)',
			  		transition: 'all 0.3s'
			  };
			  setTimeout(()=>{  //动画延时
			  	this.closeCount ++;
				if(this.closeCount===this.showCount){
					this.messageContentStyle = {};
					for(let c=0; c<this.showCount; c++){
						this.msgList[1]?this.msgList[1].class = '':'';
						this.msgList.shift();
					}
					this.mask = false;
					this.showCount = 0;	
					this.closeCount = 0;
				}
			  },300)
			  	
			},time?time:this.time)
			  
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
		z-index:9999;
		background-color:transparent;
	}
	.quick-message-centre{
		width:80%;
		height:auto;
		position:fixed;
		top:0;
		left:50%;
		transform:translate(-50%,0);
		z-index:9999;
		pointer-events: none;
		background-color:transparent;
		padding:40rpx 0;
		.quick-message-list{
            width:auto;
			height:auto;
			text-align:center;
			.quick-message-list-child{
				display:inline-block;
				word-break: break-all;
				word-wrap:break-word;
				padding:10rpx 20rpx;
				box-shadow: 0 1px 10px rgba(0,0,0,.2);
				border-radius:10rpx;
				margin-top:25rpx;
				pointer-events: auto;
				background-color:transparent;
				opacity:1;
				font-size:28rpx;
				.msg-child-content{
					display:flex;
					flex-direction:row;
					align-items:center;
					.msg-text{
						padding:0 10rpx;
					}
				}
			}
			.close-message{
			   opacity:0;
			   transition: all .3s;
			}			
			.show-message{
			   animation: messageAni .3s;
			}
			/** show **/
			@keyframes messageAni
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
			
	
			/** 颜色主题 **/
			.default-message{
				background-color:#FFFFFF;
				color:#606266;
			}
			.success-message{
				background-color:#f0f9eb;
				color:#67c23a;
			}
			.warning-message{
				background-color:#fdf6ec;
				color:#e6a23c;
			}
			.error-message{
				background-color:#fef0f0;
				color:#f56c6c;
			}			
		}
							
	}
	
  }
</style>