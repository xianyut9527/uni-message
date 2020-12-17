<template>
	<view class="quick-message">
		<view class="quick-message-mask" v-for="(item,index) in msgList" :key="index" v-show="item.mask"></view>
		<view class="quick-message-centre" :style="[messageAniStyle,directionStyle[direction].style]">
			<view class="safeHeight" :style="{height:safeHeight + 'px'}"></view>
			<view class="quick-message-list" :class="['classList' + (index+1)]" v-for="(item,index) in msgList" :key="index">
				<view class="quick-message-list-child" :style="[item.customStyle]" :class="[item.type + '-message',item.class?item.class:'']">
					<view class="msg-child-content">
						<icon class="msg-icon" :type="item.icon" :size="item.iconSize" :color="item.iconColor?item.iconColor:''" v-if="item.icon" />
						<text class="msg-text" :style="{fontSize:item.textSize,color:item.textColor}">{{item.msg}}</text>
					</view>

				</view>
			</view>
			<view class="safeHeight" :style="{height:safeHeight + 'px'}"></view>
		</view>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				msgList: [], //消息列表
				time: 3000, //默认message时长
				id: 101, //message id
				elHeight: [], //所有盒子高度
				messageAniStyle: null, //动画样式
				closeInfo: {}, //关闭消息盒子
				direction: 'top', //方向
				safeHeight: 0, //安全高度
				count: { //计数器
					start: 0,
					end: 0
				},
				color: {
					success: '#67c23a',
					default: '#1989fa',
					warning: '#e6a23c',
					error: '#f56c6c'
				},
				icon: {
					success: 'success',
					default: 'info',
					warning: 'warn',
					error: 'clear'
				},
				directionStyle: { //方向样式控制
					top: {
						class: 'show-message',
						style: {
							top: '5%'
						}
					},
					center: {
						class: 'show-message-reverse',
						style: {
							top: 'inherit',
							bottom: '50%'
						}
					},
					bottom: {
						class: 'show-message-reverse',
						style: {
							top: 'inherit',
							bottom: '10%'
						}
					}
				}
			}
		},
		mounted() {
			let that = this;
			uni.getSystemInfo({ //安全高度
				success: function(e) {
					that.safeHeight = e.safeArea.top + 'px';
				}
			})

		},
		methods: {
			/**
			 * @param {Object} options 参数配置
			 */
			show(options) {
				options.id = this.createId(); //创建id
				this.direction = options.direction || 'top'; //默认顶部显示
				options.customStyle = options.customStyle || {}; //默认自定义样式
				let fontSizeNum = options.customStyle.fontSize ? Number(options.customStyle.fontSize.toString().replace(/\D/g, '')) :
					32; //样式fontsize
				options.type = options.type ? options.type : 'default';
				let iconColor = options.iconColor ? options.iconColor : options.customStyle.color ? options.customStyle.color :
					this.color[options.type]; //color
				options.class = this.directionStyle[this.direction].class;
				options.icon = options.icon === false ? '' : options.icon !== true && options.icon ? options.icon : this.icon[
					options.type];
				options.iconColor = iconColor;
				options.textColor = options.customStyle.color ? options.customStyle.color : options.type === 'default' ? '#222222' :
					this.color[options.type];
				options.textSize = fontSizeNum + 'rpx';
				!options.iconSize ? options.iconSize = (fontSizeNum / 2) : '';
				if (this.direction !== 'top' && this.msgList.length) this.msgList[0].class = '';
				setTimeout(() => { //延迟加载
					this.direction === 'top' ? this.msgList.push(options) : this.msgList.unshift(options);
					this.closeInfo[options.id] = options;
					this.closeMessage(options);
				}, 50)

			},
			getClass(className) {
				const query = uni.createSelectorQuery().in(this);
				return new Promise((resolve, rej) => {
					query.select('.' + className).boundingClientRect(data => {
						resolve(data);
					}).exec();
				})
			},
			closeMessage(options) {
				let timeNum = !isNaN(Number(options.time)) ? Number(options.time) : this.time;
				if (options.time === false) return;
				setTimeout(async () => {
					let dataA = [],
						dataB = []; //不自动关闭数据
					this.msgList.forEach((item, index) => {
						item.time === false ? dataB.push(item) : dataA.push(item);
					})
					this.msgList = dataA.concat(dataB);
					let msgListLen = this.msgList.length; //list长度
					let lastLen = msgListLen - 1 - this.count.start;
					this.msgList = this.msgList.map((item, index) => {
						if (this.direction === 'top') {
							if (index <= this.count.start) item.class = 'close-message';
						} else {
							if (index >= lastLen) item.class = 'close-message';
						}
						return item;
					})
					this.msgList.push();
					this.count.start++;
					let messageList = null;
					if (this.direction === 'top') {
						messageList = await this.getClass("classList" + this.count.start);
					} else {
						let lastListIdx = this.msgList.length;
						messageList = await this.getClass("classList" + lastListIdx);
					}
					this.elHeight.push(messageList ? messageList.height : 41);
					let elHeight = this.elHeight.reduce(function(a, b) {
						return Number((a + b).toFixed(2))
					})
					let yNum = this.direction === 'top' ? -elHeight : elHeight;
					this.messageAniStyle = {
						transform: 'translate(-50%, ' + yNum + 'px)',
						transition: 'all 0.4s'
					};
					setTimeout(() => { //动画延时
						this.count.end++;
						if (this.count.start === this.count.end) {
							this.messageAniStyle = {};
							this.msgList = this.msgList.map((item, index) => { //清空后续显示动画解决跳动
								item.class = '';
								return item;
							})
							let msgListLen = this.msgList.length - 1;
							if (this.direction === 'top') {
								this.msgList.splice(0, this.count.start);
								this.elHeight.splice(0, this.count.start);
							} else {
								this.msgList.splice(-this.count.start, this.count.start);
								this.elHeight.splice(-this.count.start, this.count.start);
							}
							this.count.start = 0;
							this.count.end = 0;
						}
					}, 300)
				}, timeNum)

			},
			createId() { //创建id
				return ++this.id;
			},
			close(id) { //关闭指定层
				if (id && this.closeInfo[id]) {
					let item = this.closeInfo[id];
					item.time = 10;
					this.closeMessage(item);
				}

			}

		}
	}
</script>

<style lang="scss">
	.quick-message {
		background-color: transparent;

		.quick-message-mask {
			width: 100%;
			height: 100vh;
			position: fixed;
			top: 0;
			left: 0;
			z-index: 9999998;
			background-color: transparent;
		}

		.quick-message-centre {
			width: 80%;
			height: auto;
			position: fixed;
			top: 0;
			left: 50%;
			transform: translate(-50%, 0);
			z-index: 9999999;
			pointer-events: none;
			background-color: transparent;
			padding: 20rpx;

			.quick-message-list {
				width: auto;
				height: auto;
				text-align: center;

				.quick-message-list-child {
					display: inline-block;
					word-break: break-all;
					word-wrap: break-word;
					padding: 10rpx 20rpx;
					box-shadow: 0 1px 10px rgba(0, 0, 0, .2);
					border-radius: 10rpx;
					margin-top: 25rpx;
					pointer-events: auto;
					background-color: transparent;
					opacity: 1;
					font-size: 28rpx;
					text-align: left;

					.msg-child-content {
						display: flex;
						flex-direction: row;
						align-items: center;

						.msg-icon {
							padding: 0 8rpx;
						}

						.msg-text {
							padding: 0 10rpx;
							font-size: 28rpx;
						}
					}
				}

				.close-message {
					opacity: 0;
					transition: all .4s;
				}

				.show-message {
					animation: messageAni .3s;
				}

				.show-message-reverse {
					animation: messageAniReverse .3s;
				}

				/** show **/
				@keyframes messageAni {
					0% {
						opacity: 0;
						transform: translate(0, -60rpx);
					}

					100% {
						opacity: 1;
						transform: translate(0, 0rpx);
					}
				}

				@keyframes messageAniReverse {
					0% {
						opacity: 0;
						transform: translate(0, 60rpx);
					}

					100% {
						opacity: 1;
						transform: translate(0, 0rpx);
					}
				}

				/** 颜色主题 **/
				.default-message {
					background-color: #FFFFFF;
				}

				.success-message {
					background-color: #f0f9eb;
				}

				.warning-message {
					background-color: #fdf6ec;
				}

				.error-message {
					background-color: #fef0f0;
				}
			}

		}

	}
</style>
