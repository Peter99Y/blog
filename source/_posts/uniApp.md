---
title: uniApp
---

# 创建

## 命令行创建

-   `npx degit dcloudio/uni-preset-vue#vite-ts your-project`;
-   `npm run dev:mp-weixin`;
-   打包文件 dist/dev/mp-weixin;
-   打开微信开发者工具，导入 mp-weixin 文件即可;
-                             如果是ts，还需要安装ts声明文件

`npm i -D @types/wechat-miniprogram @uni-helper/uni-app-types`;

```
// tsconfig.json

{
	"compoilerOptions": {
		"types": {
			"@types/wechat-minigrogram",
			"@uni-helper/uni-app-types",
		}
	},
	"vueCompilerOptions":{
		"experimentalRuntimeMode": "runtime-uni-app"
	}
}
```

## HBuilder 创建

![](/images/uniApp/create.png)

#### h5 运行

点击运行到浏览器/内置浏览器即可

#### 小程序运行

-   运行 > 运行设置: 配置运行到小程序的启动设置；
-   如运行到微信小程序自动打开，需要将设置 > 安全设置 > 服务端口；
-   微信小程序本地联调接口时，详情 -> 本地设置 -> 不校验合法域名；

![](/images/uniApp/start.png)
![](/images/uniApp/start1.png)

#### 小程序后台账号配置

-   AppSecret(小程序密钥)，小程序有支付功能时需要添加；
-   上线时，需要在小程序服务器设置配置 request 合法域名才能被请求到；
-   上线时，downLoadFile 合法域名（如小程序有将远程图片保存到相册的功能）；
-   上线时，uploadFile 合法域名（如小程序有将图片上传到远程服务器的功能）；

# 内置组件

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

### scroll-view

可滚动区域
如页面有导航，页面主要滚动区域 和 底部 tabbar 区域，只需要计算导航，剩下的就是滚动区域高度，tabbar 不必再计算；

```
<template>
  <!-- 自定义导航栏 -->
  <CustomNavBar />

  不必计算底部tabbar高度
  <scroll-view class="scroll-view" scroll-y>
    <!-- 轮博图 -->
    <!-- 商品类型 -->
  </scroll-view>
</template>

<style lang="scss">
page {
  background-color: #f7f7f7;
  height: 100%;
  display: flex;
  flex-direction: column;

  .scroll-view {
    flex: 1; // 剩余高度
  }
}
</style>
```

### navigator

对应 uni.navigateTo 的 Api

```
	<navigator url="/pages/index/index?id=1" hover-class="navigator-hover" open-type="navigate">
		<button type="default">跳转到新页面</button>
        <image src="#"></image>
	</navigator>
```

# 生命周期

onLaunch 应用初始化完成时触发（全局只触发一次），可执行判断本地是否有缓存，验证 token，获取用户登录信息等
onLoad > onShow > onReady(可获取到 DOM 节点);

> 与 vue 生命周期执行顺序：onLoad > onShow > onBeforeMount > onReady > onMounted

# components

-   非同名目录/同名文件.vue 形式，需在 pages.json 中注册全局自定义组件

```
// pages.json

{
  // 组件自动引入规则
  "easycom": {
    // 是否开启自动扫描 @/components/$1/$1.vue 组件
    "autoscan": true,
    // 以正则方式自定义组件匹配规则
    "custom": {
      // uni-ui 规则如下配置
      "^uni-(.*)": "@dcloudio/uni-ui/lib/uni-$1/uni-$1.vue",

      // 以 Xtx 开头的组件，在 components 目录中查找
      "^Xtx(.*)": "@/components/Xtx$1.vue"
    }
  }
}
```

-   创建同名目录/同名文件.vue 形式，可直接使用全局自定义组件；

在项目根目录下创建文件 components，按照 components/目录名/文件名.vue 格式创建自定义组件；
在其他页面无需先引入注册 import，且无需配置 pages.json；

![](/images/uniApp/components.png)

-   创建 ts 文件，添加组件类型声明（这个 ts 文件无需导入）

```
// src/types/components.d.ts

import RabbitSwiper from '@/components/RabbitSwiper/RabbitSwiper.vue'

declare module 'vue' {
  export interface GlobalComponents {
    RabbitSwiper: typeof RabbitSwiper
  }
}
```

# pages.json

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

# manifest.json

### AppId

-   manifest.json > 微信小程序配置 > AppId；
-   小程序中，预览，真机调试和上传无法操作是因为没有填写 AppId;
-   小程序启动报错缺少 app.json，因为没有添加 AppId 生成;

### h5

manifest.json > Web 配置 > 网站标题等；

# vite.config.js

配置 plugin 插件，server，css，打包 build 等

### 跨域

1. h5 开发时的跨域问题，后端配置 cores 允许跨域；
2. 在 hbuilder 运行到内置浏览器；
3. server 中配置 proxy 代理；

# css

在小程序中 !important 添加的 css 权重无效，使用:deep()改变子组件样式；

在小程序中 page 根标签相当于 web 端的 body 根标签一样；

# pinia

```
// 定义 Store
export const useMemberStore = defineStore(
  'member',
  () => {
    const profile = ref<any>()
    return {
      profile,
    }
  },
  {
    <!-- persist: true, // 布尔类型只对h5有效，小程序无效；需重新需配置 -->
    persist: {
      storage: {
        getItem(key) {
          return uni.getStorageSync(key)
        },
        setItem(key, value) {
          uni.setStorageSync(key, value)
        },
      },
    },
  },
)
```

# 分包和预下载

小程序分包：将小程序的代码分割成多个部分，分别打包成多个小程序包，减少小程序的加载时间，提高用户体验；
分包预下载：在进入小程序某个页面时，由框架自动预下载可能需要的分包，提升后续分包页面时的启动速度；

# 打包上线

-   运行打包命令 > 把打包的 dist/build/mp-weixin 导入到开发者工具 > 上传 > 公众号点击提交审核

-   h5 打包后白屏问题(引用路径问题) > manifest.json > h5{router:{base: './'}}
