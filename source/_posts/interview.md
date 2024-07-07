---
title: Interview
---

## css - 圣杯布局

![](/images/interview/shengbei.png)

## css - BFC

BFC 是一个独立的布局环境，BFC 内部的元素布局与外部互不影响；

## Eventloop

js 是一门**单线程**执行的编程语言，执行任务队列中同一时间只能执行一件事情。如果前一个任务非常耗时，后面的任务会一直等待，导致程序假死状态。

-   为了防止程序假死问题，js 把待执行的任务分为了 2 类： 1.同步任务：在 js 主线程上排队执行的任务。 2.异步任务：由 js 委托给环境(浏览器/node.js)进行执行，当异步任务执行完成后，会通知 js 主线程执行异步任务的回调函数。

![](/images/interview/1.jpeg)

JavaScript 主线程从“任务队列”中读取异步任务的回调函数，放到执行栈中依次执行。这个过程是循环不断的，所以整个的这种运行机制又称为 EventLoop (事件循环)

![](/images/interview/2.jpeg)

---

## 宏任务 & 微任务

js 把异步任务做了进一步的划分，分为 2 类：

-   宏任务：script(整体代码), Ajax, setTimout, setInterval, 文件操作等
-   微任务：Promise.then/catch/finally 方法（new Promise()是同步任务）, MutationObserver, process.nextTick 等

![](/images/interview/3.jpeg)

![](/images/interview/4.jpeg)

解析：1 是异步宏任务，2 是同步任务，3 是异步微任务，4 是同步任务；
先执行 script 宏任务，优先执行同步任务（2、4），再执行异步微任务（3），所有异步微任务执行完后，最后执行 setTimeout 宏任务（1）；

了解宏任务和微任务可以解决：
js 操作数据是同步的，但 vue 操作 js 更新 DOM 是异步的，此时获得的 DOM 上的数据是之前的；
通过 nextTick 将异步任务的微任务优先执行，再执行下一个异步宏任务；

---

## 什么是前端工程化？

指的是 4 个现代化，对工具、技术、流程等进行规范、标准化。

-   模块化：js 的 ES6 模块化，css 的模块化(less,sass)等。
-   组件化：封装可服用的 html、css、js 等
-   规范化：借口规范，文档规范，git 分支管理，编码规范等
-   自动化：自动化构建、部署、测试等

工程化解决方案：webpack，vite，gurp 等。它提供了前端模块化开发，代码压缩，处理浏览器端兼容性，性能优化等功能。

---

## &lt;tempate&gt;

1. 是 vue 提供的容器标签，只起到包裹的作用，不会被渲染为真正的 DOM 元素。
   所以在&lt;template&gt;标签上进行循环的时候，无法绑定 key。

2. vue2 中 template 节点仅支持单个根节点; vue3 中组件根节点支持了多个根结点。

---

## 防抖

```
let timer = null;
function debounce(callback, delay = 500) {
    // 清除之前的计时;
	clearTimeout(timer);

	timer = setTimeout(function () {
		callback();
	}, delay);
}

window.onresize = function () {
	debounce(function () {
		console.log("debounce");
	}, 500);
};
```

-             解决全局污染

```
function debounce(callback, delay = 500) {
	let timer = null;

    // 利用闭包返回新的函数，持有局部作用域下的变量timer
	return function () {
		// 清除之前的计时;
		clearTimeout(timer);

		timer = setTimeout(function () {
			callback();
		}, delay);
	};
}

let handle = debounce(function () {
	console.log("debounce");
}, 500);

window.onresize = function () {
	handle();
};
```

-   防抖传参

```
function debounce(callback, delay) {
	let timer = null;

	// 利用闭包返回新的函数，持有局部作用域下的变量timer
	return function () {
		// 清除之前的计时;
		clearTimeout(timer);

		// 接受传递的所有参数
		let args = arguments;

		timer = setTimeout(function () {
			callback.apply(null, args);
		}, delay);
	};
}

let handle = debounce(function (first, second) {
	console.log(first, second);
}, 500);

window.onresize = function () {
	let clientWidth = document.documentElement.clientWidth;
	let clientHeight = document.documentElement.clientHeight;
	handle(clientWidth, clientHeight);
};
```

## 节流

```
let timer = null;

function throttle(cb, delay) {
	if (timer) return;

	timer = setTimeout(() => {
		cb();
		timer = null;
	}, delay);
}

window.onresize = function () {
	throttle(function () {
		console.log("throttle");
	}, 1000);
};
```

-   解决全局污染

```
function throttle(cb, delay) {
	let timer = null;

	return function () {
		if (timer) return;
		timer = setTimeout(() => {
			cb();
			timer = null;
		}, delay);
	};
}

let handle = throttle(function () {
	console.log("throttle");
}, 1000);

window.onresize = function () {
	handle();
};
```

-   节流穿参

```
function throttle(callback, delay) {
	let timer = null;

	return function () {
		if (timer) return;
		let args = arguments;

		timer = setTimeout(() => {
			callback.apply(null, args);
			timer = null;
		}, delay);
	};
}

let handle = throttle(function (first, second) {
	console.log(first, second);
}, 1000);

window.onresize = function () {
	let clientWidth = document.documentElement.clientWidth;
	let clientHeight = document.documentElement.clientHeight;
	handle(clientWidth, clientHeight);
};
```

## 浅拷贝 & 深拷贝

浅拷贝只是拷贝第一层, 而像引用类型数组、对象，就只是拷贝引用地址;

```
// 浅拷贝：也可用for循环
var copedObj = {name: 'tom', children:[1,2]};
var targetObj = {};

Object.assign(targetObj, copedObj)
```

```
// 深度拷贝
function deepClone(obj = {}) {
	if (typeof obj !== "object" || obj == null) return obj;

	let result;
	if (obj instanceof Array) {
		result = [];
	} else {
		result = {};
	}

	for (let key in obj) {
		// 只获取对象自身属性，否则原型链上的属性也会被复制;
		if (obj.hasOwnProperty(key)) {
			result[key] = deepClone(obj[key]);
		}
	}
	return result;
}
```

## 性能优化

First Contentful Paint: 首次内容绘制的时间，浏览器第一次绘制 DOM 相关的内容，也是用户第一次看到页面内容的时间。
Speed Index: 页面各个可见部分的显示平均时间，当我们的页面上存在轮播图或者需要从后段获取内容加载时，这个数据会受到影响。
Largest Contentful Paint: 最大内容绘制时间，页面最大的元素绘制完成的时间。
Time to Interactive: 从页面开始渲染到用户可以与页面进行交互的时间（内容渲染完毕，交互元素绑定的事件注册成功）。
Total Blocking Time: 主进程被阻塞的时间（记录了首次内容绘制到用户可交互之间的时间内，主进程被阻塞导致阻碍用户的无法交互）

![](/images/interview/lighthouse.jpg)

###### 分析打包后的代码体积

需安装插件 npm install rollup-plugin-visualizer 分析打包后的代码体积
请查看其他别人的文档；

-   vite 可配置优化

`vite.config.ts`

```
defineConfig({
	build:{
		cssCodeSplit: true, // css代码拆分
		sourcemap: false // 不生成sourcemap
		minify: false, // 'esbuild'-打包速度快，'terser'-打包体积最小
		assetsInlineLimit: 4000, // 不会打包小于的图片，小于4000kb的体积将被编译成base64
	}
})
```

## 首屏加载优化

-   路由懒加载 import 导入组件；
    vue 是单页应用，打包时，所有的 js 都会打包成一个 js 文件会变得非常大，影响页面加载速度，使用 import 导入组件可以把路由对应的组件分割成不同的代码块，用户访问对应的路由才加载对应的组件；

-   vue3 新增 defineAsyncComponent 异步组件，同样可以 npm run build 时将一个包分包，用到这个 js 时才会加载这个包；

-   vuex 状态管理；

-   图片懒加载 和 小图片采用 base64 字符串方式；

-   第三方插件采用 CDN 方式引入；

-   虚拟列表（当后端返回上万条数据时）；

-   防抖和节流；

-   PWA；

    需安装 npm install vite-plugin-pwa -D
    利用 service worker 离线缓存, 利用 service worker 发送通知；

## vue3 与 vue2 区别

1.  vue2 选项式写法，在 data 中定义的属性，methods，computed，watch 中都会使用，造成代码结构是分散不相关的；组合式 api 则可以统一写在一块，高内聚；
2.  选项式与 ts 结合并不友好，组合式与 ts 结合更友好，比如类型推断；

3.  但 vue3 组合式 api 也造成一个问题，就是它不像 vue2 这种强制的帮你划分了结构，methods 里的方法都在 methods 对象里面，computed 里的方法都在 computed 里面，以及 watch、filter 等等。也就造成了 vue3 代码最后写出来很乱，所有的东西都在一块；解决方案就是拆分业务，根据业务把同一个组件中独立的业务代码抽象封装成函数。一个页面每一块的业务都可以独立成一个函数导入调用。
    -   按照业务声明以“use”开头的逻辑函数；
    -   把独立的业务逻辑封装到各个函数内部；
    -   函数内部把组件中需要用到的数据和方法以对象的形式 return 出去；
    -   在组件中导入函数，把对应的数据或方法解构使用即可；

```
<script setup>
import { useBanner } from "./components/useBanner";

const { bannerList } = useBanner();
</script>

<template>
	<div v-for="it of bannerList">{{it.name}}</div>
</template>

<style scoped></style>
```

useBanner.js

```
// banner数据
import { getBannerAPI } from "@/apis/home";
import { onMounted, ref } from "vue";

export function useBanner() {
	const bannerList = ref([]);
	const getBanner = async () => {
		const res = await getBannerAPI({ site: 2 });
		bannerList.value = res.result;
	};

	onMounted(() => getBanner());

	return {
		bannerList,
	};
}
```

## vue3 新特性

1. 重写了双向绑定数据;
   vue2 使用 Object.defineProperty 对属性做的监听，通过 get 和 set 做了对象属性的劫持；vue3 通过 ES6 的 Proxy 做的数据代理；
   为什么要换成 proxy？defineProperty 是对于对象属性的监听，而对于数组 vue2 是自己重写了数组的方法；vue3 通过 proxy 可以对数组比较友好；

<!-- 2. 提升了 VDOM 性能 -->

3. vue3 支持了多个根节点，vue2 只能有一个根节点；

4. v-model 支持了多个 v-model，vue2 只能是一个，如果要实现像多个 v-model 数据的修改效果，只能绑定多个属性，在子组件通过$emit 触发每个对应的自定义事件去修改父组件数据；

<!-- 5. Tree-shaking 的支持， -->

6. 支持了组合式 API；

## 虚拟 DOM

1. 虚拟 DOM 就是通过 js 生成一个*抽象语法树*。比如 ts 转 js 的时候，在转换的过程中也会抽象语法树的转换；Babel 这个插件在 ES6 语法转 ES5 的时候，也会进行抽象语法树的转换；

2. 为什么不直接操作 DOM 呢？因为一个 DOM 它的属性是非常多的，直接操做 DOM 是非常浪费性能；
   有了虚拟 DOM 之后可以做一些优化，比如一些 DOM 可以做一些复用，然后就产生了 diff 算法，通过 diff 算法的对比，让一些 DOM 被复用，也就优化了性能。
3. diff 算法导致组件被复用，比如在切换的时候，循环的时候需要添加一个属性，并给它赋唯一值；

## scoped 原理

1. 给 HTML 的 DOM 节点加一个不重复 data 属性（data-v-N）来表示它的唯一性；
2. 在 css 选择器（如.box{width: 1px }）的末尾加一个当前组件的 data 属性选择器（data-v-N）来私有化样式（.box{width: 1px} => .box[data-v-N]{width: 1px}；
3. 如组件内部包含了其他子组件，只会给子组件的最外层标签上添加当前组件的 data 属性；

```
<template>
	<div class="container">
		<el-input />
	</div>
</template>

<style scoped>
.container {
	border: 1px solid orange;
}

/*
	无效，因为是第2条原理，在css选择器末尾加一个当前组件的data属性选择器
	.el-input .el-input__inner[data-v-N] {}

	.el-input .el-input__inner {
			border: 1px solid red;
	}
*/


/*
	有效 deep样式穿透将data属性提前到跟随的css选择器末尾
	.el-input[data-v-N] .el-input__inner {}
*/
.el-input :deep(.el-input__inner) {
	border: 1px solid red;
}
</style>
```

![](/images/interview/scoped.jpg)

## vue-router 跳转实现

1. hash 模式：URL 会带有#，它是通过 BOM 提供的 location.hash 实现的， 通过 window.addEventListener('hashchange',(e)=>{})监听 url 变化

```
location.href="新url"
location.assign("新url")  也可实现在当前窗口打开，可后退
location.replace("新url")  实现禁止后退      原理: 用新url替换history中旧的url
```

2. history 模式：通过 BOM 提供的 history 函数，window.addEventListener('popstate', (e)=>{})监听 url 变化；

```
history.forward()  ==  history.go(1) 前进一步
history.back()   ==  history.go(-2) 后退两步
history.go(0) 刷新
```

## 发布订阅模式

设计模式包含：工厂模式，观察者模式，构造器模式，发布订阅等

-   vue2 的 eventBus 是发布订阅模式，通过$on监听事件，$emit 派发事件；
-   addEventListener 也是发布订阅模式

```
const fn = ()=>{
	console.log('click')
}


document.addEventListener('abc', fn, {		// 添加监听器
	once: true,	// 只触发一次
})
document.removeEventListener('abc', fn)	// 移除监听器

const e = new Event('abc');			// 订阅事件

document.dispatchEvent(e);			// 派发事件
```
