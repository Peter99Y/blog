---
title: uniApp
---

## 创建

![](/images/uniApp/create.png)

## 运行

### h5

点击运行到浏览器/内置浏览器即可

### 小程序

运行 > 运行设置: 配置运行到小程序的启动设置；
如运行到微信小程序自动打开，需要将设置 > 安全设置 > 服务端口；
![](/images/uniApp/start.png)
![](/images/uniApp/start1.png)

## @

uniApp 的@符号是相对于根目录，和 vue 项目有区别；

## 内置组件

div，h1 标签在 h5 没问题，但是运行到小程序时就无效了，所以需要写 view 和 text 替代；

### view

在移动端和 pc 交互有些差异，如 view 标签增加 hover 属性手指点击效果, hover-stop-propagation 属性阻止冒泡效果。text 标签增加长按是否可选属性 等

```
<template>
	<view class="box" hover-class="hover-box" hover-stop-propagation>
		<view class="inner" hover-class="hover-inner" hover-stop-propagation hover-stay-time="0"></view>
	</view>
</template>

<style lang="scss" scoped>
	.box {
		width: 100px;
		height: 100px;
		border: 1px solid orange;
	}

	.hover-box {
		background-color: orange;
	}

	.inner {
		width: 60px;
		height: 60px;
		border: 1px solid black;
	}

	.hover-inner {
		background-color: black;
	}
</style>
```

![](/images/uniApp/view.png)

### image

```
<template>
	<!-- scaleToFill：长和宽都会拉伸填满，多余宽度和高度会被拉伸； -->
	<image class="pic-1" src="../../static/8.jpg" mode="scaleToFill"></image>

	<!-- aspectFit：最高的的宽或高完全展示出来，另外一边会留白； -->
	<image class="pic-1" src="../../static/8.jpg" mode="aspectFit"></image>

	<!-- aspectFill：最低的的宽或高完全展示出来，另外一边会被截取； -->
	<image class="pic-1" src="../../static/8.jpg" mode="aspectFill"></image>

	<!-- widthFix：根据设置的宽度，自动设置图片高度，图片完全展示出来 -->
	<image class="pic-2" src="../../static/8.jpg" mode="widthFix"></image>

	<!-- heightFix：根据设置的高度，自动设置图片宽度，图片完全展示出来 -->
	<image class="pic-3" src="../../static/8.jpg" mode="heightFix"></image>
</template>

<style lang="scss" scoped>
	image{
		display: block;
		margin-bottom: 5px;
	}
	.pic-1 {
		width: 80px;
		height: 80px;
		border:2px solid orange;
	}
	.pic-2 {
		width: 80px;
		border:2px solid orange;
	}
	.pic-3 {
		height: 80px;
		border:2px solid orange;
	}
</style>
```

![](/images/uniApp/image.png)

### navigator

对应 uni.navigateTo 的 Api

```
	<navigator url="/pages/index/index?id=1" hover-class="navigator-hover" open-type="navigate">
		<button type="default">跳转到新页面</button>
        <image src="#"></image>
	</navigator>
```

## 生命周期

onLaunch 应用初始化完成时触发（全局只触发一次），可执行判断本地是否有缓存，验证 token，获取用户登录信息等
onLoad > onShow > onReady(可获取到 DOM 节点);

> 与 vue 生命周期执行顺序：onLoad > onShow > onBeforeMount > onReady > onMounted

## components

在项目根目录下创建文件 components，按照 components/目录名/文件名.vue 格式创建自定义组件；
在其他页面在 template 可直接使用组件，无需先引入注册 import；

![](/images/uniApp/components.png)

## pages.json

新建页面并生成页面路由 或 自己新增页面添加对应的 path🪖 即可，页面访问地址：localhost:端口号/#/pages/目录/文件名；

### needLogin

是否需要登录才可访问此页面；

### 启动展示页

数组中第一项表示应用启动页；

```
{
	"pages": [
		{
			"path": "pages/index/index",
			"style": {
				"navigationBarTitleText": "uni-app"
			},
            needLogin: true,
		},
		{
			"path" : "pages/demo1/demo1",
			"style" :
			{
				"navigationBarTitleText" : ""
			}
		}
	],
	"globalStyle": {
		"navigationBarTextStyle": "black",
		"navigationBarTitleText": "uni-app",
		"navigationBarBackgroundColor": "#F8F8F8",
		"backgroundColor": "#F8F8F8"
	},
	"uniIdRouter": {}
}
```

## manifest.json

### AppId

-   manifest.json > 微信小程序配置 > AppId；
-   小程序中，预览，真机调试和上传无法操作是因为没有填写 AppId;
-   小程序启动报错缺少 app.json，因为没有添加 AppId生成;

### h5

manifest.json > Web 配置 > 网站标题等；

## vite.config.js

配置 plugin 插件，server，css，打包 build 等

### 跨域

1. h5 开发时的跨域问题，后端配置 cores 允许跨域；
2. 在 hbuilder 运行到内置浏览器；
3. server 中配置 proxy 代理；

## css

在小程序中 !important 添加的 css 权重无效，使用:deep()改变子组件样式;
