---
title: Interview
---

#### Eventloop

js 是一门**单线程**执行的编程语言，执行任务队列中同一时间只能执行一件事情。如果前一个任务非常耗时，后面的任务会一直等待，导致程序假死状态。

-   为了防止程序假死问题，js 把待执行的任务分为了 2 类： 1.同步任务：在 js 主线程上排队执行的任务。 2.异步任务：由 js 委托给环境(浏览器/node.js)进行执行，当异步任务执行完成后，会通知 js 主线程执行异步任务的回调函数。

![](/images/interview/1.jpeg)

JavaScript 主线程从“任务队列”中读取异步任务的回调函数，放到执行栈中依次执行。这个过程是循环不断的，所以整个的这种运行机制又称为 EventLoop (事件循环)

![](/images/interview/2.jpeg)

---

#### 宏任务 & 微任务

js 把异步任务做了进一步的划分，分为 2 类：

-   宏任务：异步 Ajax, setTimout,setInterval, 文件操作等
-   微任务：Promise.then/.catch/.finnaly, process.nextTick 等

![](/images/interview/3.jpeg)

![](/images/interview/4.jpeg)

解析：1 是异步宏任务，2 是同步任务，3 是异步微任务，4 是同步任务；
先执行所有同步任务 2、4，再执行异步微任务 3，最后执行异步宏任务 1

---

#### 什么是前端工程化？

指的是 4 个现代化，对工具、技术、流程等进行规范、标准化。

-   模块化：js 的 ES6 模块化，css 的模块化(less,sass)等。
-   组件化：封装可服用的 html、css、js 等
-   规范化：借口规范，文档规范，git 分支管理，编码规范等
-   自动化：自动化构建、部署、测试等

工程化解决方案：webpack，vite，gurp 等。它提供了前端模块化开发，代码压缩，处理浏览器端兼容性，性能优化等功能。

---

#### &lt;tempate&gt;

是 vue 提供的容器标签，只起到包裹的作用，不会被渲染为真正的 DOM 元素。
所以在\&lt;template&gt;标签上进行当循环的时候，无法绑定 key。

vue2 中 template 节点仅支持单个根节点。
vue3 中组件根节点支持了多个根结点。

---

#### 浅拷贝& 深拷贝

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
		// 对象自身属性中是否有某个属性；否则原型链上的属性也会被复制;
		if (obj.hasOwnProperty(key)) {
			result[key] = deepClone(obj[key]);
		}
	}
	return result;
}
```

#### 首屏加载优化

-   路由懒加载
    vue 是单页应用，打包时，所有的 js 都会打包成一个 js 文件会变得非常大，影响页面加载速度，使用 import 导入组件可以把路由对应的组件分割成不同的代码块，用户访问对应的路由才加载对应的组件；
-   vue3 - defineAsyncComponent 异步组件加载；
-   vuex 状态管理；
-   图片懒加载；

#### 生命周期

-   created: 编译模版；
-   mounted: 挂载模版；
-   updated: 在组件的任意 DOM 节点更新后调用；
    注意：仅限用差值语法使用在页面上的 data 属性，若没有使用在页面上，自身的 updated 或子组件的 updated 都不会触发；

#### vue3 composition Api

-   选项式写法，在 data 中定义的属性，methods,computed,watch 中都会使用，造成代码结构是不相关的；组合式 api 则可以统一写在一块，高内聚；
-   选项式与 ts 结合并不友好，组合式与 ts 结合更友好，比如类型推断；

#### css - 圣杯布局

![](/images/interview/shengbei.png)

#### css - BFC

BFC 是一个独立的布局环境，BFC 内部的元素布局与外部互不影响；
