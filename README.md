
## 小巧灵活的全局message toast 消息提示
**全局message toast 消息提示封装 自定义扩展强,默认icon为uni官方icon(如需添加其他icon可自行扩展)**

## 使用说明
### 组件引入 
####man.js
```javascript
import Vue from 'vue'
import App from './App'

import message from '@/components/quick-message/quick-message.vue'
Vue.component('quick-message',message);
```
#### 或者pages.json
```javascript
{
	"easycom": {
			"^quick-(.*)": "@/components/quick-$1/quick-$1.vue"
	},
	"pages": [...],
	...
}
```

2种引入方式任选一种都可以

#### vm引入
```html
<template>
 <view>
   <view @click="showMessage">show</view>
   <!-- 全局message组件 -->
   <quick-message ref="message"></quick-message>
 </view>
</template>
<script>
export default {
		data() {
			return {
			  ...
			}
		},
		methods:{
			showMessage(){ //显示message
					this.$refs.message.show({
						type:'warning', //String 默认default
						msg:'点击了消息提示窗', //String 显示内容 *
						icon:false, //Boolean|String 显示icon(false/true/string 默认显示icon)
						mask:true, //Boolean 遮罩（默认false没有遮罩）
						time:5000, //Number|Boolean 默认3000/false为不自动关闭
						customStyle:{ //自定义样式
						 fontSize:32, //Number|String (单位rpx)						 color:'#FFFFFF', //字体图标色
						 backgroundColor:'#F96' //背景色
						 ...
						}, 
						iconSize: 18, //Number 自定义icon大小(单位px 默认16 设置后会覆盖自定义样式里的设置优先级最高)
						iconColor: '', //String icon颜色(默认主题颜色 设置后会覆盖自定义样式里的设置优先级最高)
					})
				}
		}
}
</script>
...
```
如果想扩展icon可在icon处输入icon name 支持uni官方icon
官方icon链接：<https://uniapp.dcloud.io/component/icon>

### 基本使用
```javascript
    this.$refs.message.show({
		 msg:'基础提示'
	})
```

### 图片 Images
![Pandao editor.md](https://s1.ax1x.com/2020/08/02/athExx.png "Pandao editor.md")
### License
[MIT](https://opensource.org/licenses/MIT)
### End