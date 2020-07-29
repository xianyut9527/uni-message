<template>
  <view class="quick-message">
	<view class="quick-message-mask" v-for="(item,index) in msgList" :key="index" v-show="item.mask"></view>
	<view class="quick-message-centre" :style="[messageAniStyle,centerStyle]">
		<view class="quick-message-list" :class="['classList' + (index+1)]" v-for="(item,index) in msgList" :key="index">
		  <view class="quick-message-list-child" :style="item.customStyle" :class="[item.type + '-message',item.class?item.class:'']">
			   <view class="msg-child-content">
				 <icon class="msg-icon" :type="item.icon" :size="item.iconSize?item.iconSize:16" :color="item.iconColor?item.iconColor:''" v-if="item.icon"/>
				 <text class="msg-text" :style="{fontSize:item.textSize,color:item.textColor}">{{item.msg}}</text>  
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
			  id:101,
			  closeInfo:{},
			  centerStyle:{
				 top:0
			  },
			  messageAniStyle:null,
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
			  }
			}
		},
	    mounted(){
		 let that = this;	
		 uni.getSystemInfo({
		        success: function(e) {
					that.centerStyle.top = e.safeArea.top + 'px';
				}
		 })
				
		},
		methods:{
          show(options){ 
			/**
			 * 参数配置
			 */  
			options.id = this.createId();
			options.type = options.type?options.type:'default';
			options.class = 'show-message' //show
			options.icon = options.icon===false?'':options.icon!==true&&options.icon?options.icon:this.icon[options.type];
			if(options.customStyle&&options.customStyle.color){
				!options.iconColor?options.iconColor = options.customStyle.color:'';
				options.textColor = options.customStyle.color;
			}
			if(options.customStyle&&options.customStyle.fontSize){
				let fontSizeNum = Number(options.customStyle.fontSize.toString().replace(/\D/g,''));
				!options.iconSize?options.iconSize = parseInt(fontSizeNum/2.2):'';
				options.textSize = fontSizeNum + 'rpx';
			}
			options.respond&&options.mask!==false?options.mask = true:'';
			!options.iconColor?options.iconColor = this.color[options.type]:'';
			!options.textColor?options.textColor = options.type==='default'?'#222222':this.color[options.type]:'';
			this.msgList.push(options);
			this.closeMessage(options);
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
		  closeMessage(options){  
			let timeNum = options.time?options.time:this.time;
			if(options.respond&&!this.closeInfo[options.id]){
				this.closeInfo[options.id] = options;
				return;
			}  
			setTimeout(async ()=>{
			  let dataA = [];
			  let dataB = []; //不自动关闭数据
			  this.msgList.forEach((item,index)=>{
				item.respond?dataB.push(item):dataA.push(item); 
			  })
			  this.msgList = dataA.concat(dataB);
			  this.msgList = this.msgList.map((item,index)=>{
				  index<=this.showCount?item.class = 'close-message':'';
				  return item;
			  })
			  this.msgList.push();
			  this.showCount ++;
			  let countBs = this.showCount?this.showCount:1;
			  let messageList = await this.getClass("classList" + this.showCount);
			  let yNum = -Number((messageList.height * countBs).toFixed(2));
			  this.messageAniStyle = {
			  		transform: 'translate(-50%, '+yNum+'px)',
			  		transition: 'all 0.3s'
			  };
			  setTimeout(()=>{  //动画延时
			  	this.closeCount ++;
				if(this.closeCount===this.showCount){
					this.messageAniStyle = {};
					for(let c=0; c<this.showCount; c++){
						this.msgList = this.msgList.map((item,index)=>{ //清空后续显示动画解决跳动
							item.class = '';
							return item;
						})
						this.msgList.shift();
					}
					this.showCount = 0;	
					this.closeCount = 0;
				}
			  },300)
			  	
			},timeNum)
			  
		  },
		  createId(){
			this.id ++;
			let id = this.id; 
			return id;
		  },
		  close(id){ //关闭指定层
		    if(id&&this.closeInfo[id]){
			   let item = this.closeInfo[id];	
			   item.time = 10;
			   item.respond = false;
			   this.closeMessage(item);
			}
			
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
		padding:20rpx 0;
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
				text-align:left;
				.msg-child-content{
					display:flex;
					flex-direction:row;
					align-items:center;
					.msg-icon{
						padding:0 8rpx;
					}
					.msg-text{
						padding:0 10rpx;
						font-size:28rpx;
					}
				}
			}
			.close-message{
			   opacity:0;
			   transition: all .2s;
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
		}
							
	}
	
  }
</style>