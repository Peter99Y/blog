---
title: 面经
---


#### Eventloop
js是一门**单线程**执行的编程语言，执行任务队列中同一时间只能执行一件事情。如果前一个任务非常耗时，后面的任务会一直等待，导致程序假死状态。

* 为了防止程序假死问题，js把待执行的任务分为了2类：
1.同步任务：在js主线程上排队执行的任务。
2.异步任务：由js委托给环境(浏览器/node.js)进行执行，当异步任务执行完成后，会通知js主线程执行异步任务的回调函数。

![](https://upload-images.jianshu.io/upload_images/20409039-a1305218bb17f29a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

JavaScript 主线程从“任务队列”中读取异步任务的回调函数，放到执行栈中依次执行。这个过程是循环不断的，所以整个的这种运行机制又称为 EventLoop (事件循环)

![](https://upload-images.jianshu.io/upload_images/20409039-7fc6fa6de775ab46.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

---
#### 宏任务 & 微任务
js把异步任务做了进一步的划分，分为2类：
* 宏任务：异步Ajax, setTimout,setInterval, 文件操作等
* 微任务：Promise.then/.catch/.finnaly, process.nextTick等
![](https://upload-images.jianshu.io/upload_images/20409039-dfb22eddd286d902.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![](https://upload-images.jianshu.io/upload_images/20409039-bd3c6583e8eb5d67.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
解析：1是异步宏任务，2是同步任务，3是异步微任务，4是同步任务；
先执行所有同步任务2、4，再执行异步微任务3，最后执行异步宏任务1

---
#### 什么是前端工程化？
指的是4个现代化，对工具、技术、流程等进行规范、标准化。
* 模块化：js的ES6模块化，css的模块化(less,sass)等。
* 组件化：封装可服用的html、css、js等
* 规范化：借口规范，文档规范，git分支管理，编码规范等
* 自动化：自动化构建、部署、测试等

工程化解决方案：webpack，vite，gurp等。它提供了前端模块化开发，代码压缩，处理浏览器端兼容性，性能优化等功能。

---
#### <tempate>
是vue提供的容器标签，只起到包裹的作用，不会被渲染为真正的DOM元素。
所以在<template>标签上进行当循环的时候，无法绑定key。

vue2中template节点仅支持单个根节点。
vue3中组件根节点支持了多个根结点。
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





