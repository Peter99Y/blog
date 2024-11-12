---
title: Dom & Bom
---

### 浏览器渲染原理

#### 解析 HTML

浏览器从网络或本地文件获取到 HTML 源代码，然后从上到下逐行解析代码；
若解析过程中，读取到 css 和 js （不包含图片/视频/音频）就会停止解析，转而解析 css 和 js，解析完成后再继续之前的解析；

> 1.为什么要将 css 写到 &lt;head&gt;中，js 写到最后？
> css 写到开头：是为了让浏览器尽快读取并解析样式，让用户先看到漂亮的页面，同时也是为了避免页面闪烁；如果样式放在元素后面，元素会按默认样式先渲染，然后读取新的样式覆盖了之前读取的样式就会造成闪烁
> js 写到结尾：js 获取 DOM 元素，需要先等待 DOM 元素加载完成才能获取；js 是做交互作用，交互一般是等待 DOM 加载完成才执行；

#### 生成 DOM

浏览器一边解析 HTML，一边生成 DOM 树，在 js 中获取到的 dom 就是这里的 DOM 节点；

#### 生成渲染树

浏览器一边生成 DOM 树，一边计算 DOM 树中每个节点的样式规则，最终形成渲染树，css 属性的计算过程发生在这一步；

#### reflow 重排 (回流; 就是排列顺序)

浏览器一遍生成渲染树，一边计算每个元素最终的尺寸和位置。完成后，页面中的素有元素的位置和尺寸确定下来了，即将被渲染到页面中。
这个步骤会在页面之后的运行过程中不断地重复。

浏览器为了提升性能，对 js 中连续导致 reflow 的代码，把 reflow 的时间点延迟到结束后进行，在此过程中，如果遇到了获取尺寸和位置的代码，浏览器会立即 reflow；会导致 reflow：

- 获取元素的尺寸和位置;
- 直接或间接改变元素的尺寸和位置;

```
dom.style.width = '100px';
dom.style.height = '50px';
// dom.clientHeight;   若读取，会强行执行reflow;
dom.style.top = '50px';
最后重排只会执行一次
```

#### repaint 重绘

浏览器一边 reflow，一边生成对应的图形绘制到页面，绘制过程称为 repaint，所有会导致 reflow 都会 repaint;
不影响盒子排列，仅影响盒子外观的代码不会导致 reflow，一些会导致 repaint 如：

- 改变背景颜色
- 变更字体颜色
- 背景图
- 圆角边框

#### 扩展

```
// js放在上面先加载js资源，此时需要两种方式避免js获取不到元素而加载错误；
<head>
<script>
	document.addEventListener('DOMContentLoaded', function(){
		console.log('DOM树全部加载完成');
	})

	window/img/video.addEventListener('load', function(){
		console.log('1.页面上所有资源（包含图片资源）加载完成触发, 2.此事件是注册在window/img等上');
	})
</script>
</head>
```

### DOM

(Document Object Model)文档对象模型, 通过 DOM 可以改变页面内容、结构和样式.

![](/images/Dom&Bom/1.webp)

###### nodeType 节点类型

- 9：document;
- 1：element(元素节点);
- 2：attribute;
- 3：text(文本&空格);
- 8：comment(注释);

```
ul.firstElementChild.nodeType;  // 1
ul.firstElementChild.nodeName;  // LI
```

###### 获取元素节点 el

```
document.documentElement    // <html>
document.head               // <head>
document.body               // <body>

var ele = document.getElementById('box');

document.getElementById('ul-box');

document.getElementsByTagName('ul-box')[0];

document.getElementsByClassName('ul')[0];

document.querySelector('#ul-box');
document.querySelectorAll('li);

console.log(typeof ele)     // object
console.dir( ele )          // 查看元素对象的属性和方法
```

###### el.parentNode

获取元素父节点

```
var liTwo = document.getElementsByTagName('li')[1];
liTwo.parentNode;
liTwo.parentElement;    // 等价于
```

###### el.children, el.firstElementChild, el.lastElementChild

获取元素子节点;

```
var ul = document.getElementById('ul-box');

ul.children[1];

ul.firstElementChild;
ul.lastElementChild;
```

###### el.previousElementSibling, .nextElementSibling

获取元素兄弟节点;

```
var liTwo = document.getElementsByTagName('li')[1];

liTwo.previousElementSibling
liTwo.nextElementSibling;
```

###### el.style

注意: el.style 只能获取内联样式（标签 style 的属性值）

```
var btn = document.getElementById('btn');
var img = ele.getElementByClassName('img')[0];
var inp = document.querySelector('input');
var check = document.getElementById('checkbox');

btn.onclick = function(){
  this.style.backgroundColor = 'orange';
  this.className = 'btn btn-orange';

  img.src = './img/1.png';
  img.alt = '这是一张图片';

  inp.type = 'password';
  inp.value = '123456';
  inp.disable = true;

  check.checked = !check.checked;

  this.innerHTML = `<span>123</span>`; //1.可赋值html标签，2.空格和3.换行
  this.innerText = '你好';             // 只有文本
}
```

###### el.attributes, el.setAttribute, el.removeAttribute

```
<input type="text" v-model="msg">

var inp = document.querySelector('input');
console.log( inp.attributes );  // { 0: type, 1: v-model }
```

H5 元素规定自定义属性 data- 开头作为自定义属性名，标签自定义属性名（data-Name）

```
  if(box.getAttribute('data-index') == 1){
      box.setAttribute('data-index', 2);

      H5新增 定义属性dataset集合, 不再需要前缀data- ;   (ie 11才支持)
      console.log( box.dataset.listName);
  }else{
      box.removeAttribute('data-index');
  }
</script>
```

###### el.className

获取/修改/移除 标签属性;

```
<div
  id="box"
  class="bg"
  data-index="1"
  data-list-name="hh"
>
  hello
</div>
```

```
<script>
  var box = document.getElementById('box');

  1.元素本身的属性可直接获取/设置;
  box.id = 'box-wrapper';

  2.或
  var idValue = box.getAttribute('id');

  3.获取/设置class属性 需使用 className
  var title = box.className;
  if(title === 'bg'){
    box.className = 'change-bg'
  }
</script>
```

###### el.classList、.add、.remove、.replace、.toggle

获取/修改/移除/切换 元素属性;

```
    <script>
        let c = document.querySelector('#ccc');
        let btn = document.querySelector('button');

        c.classList.remove('bg');
        c.classList.add('small-bg', 'middle-bg', 'big-bg');

        btn.addEventListener('click', function () {
            // 如元素的class值show已存在，则移除它，否则添加它;
            c.classList.toggle('show')
        })
    </script>
```

###### el.cloneNode 克隆节点

```
var ul = document.querySelector('ul');

//node.cloneNode(false); 不传参或传false-浅拷贝, true-深拷贝
//1.复制节点，2.复制里面的所有子节点;

var li = ul.children[0].cloneNode();
ul.appendChild(li);
```

###### 创建/插入/移除节点

```
var li = document.reateElement('li');
var ul = document.getSelector('ul');

li.innerHTML = '1';
ul.appendChild(li);
ul.insertBefore( li, ul.chidren[0] );

ul.removeChild( ul.children[1] );
```

###### example

![](/images/Dom&Bom/2.webp)

```
var tbody = document.querySelector('tbody');

for(var i = 0; i < datas.length; i++){
  var tr = document.createElement('tr');
      tbody.appendChild(tr);

      var row =  datas[i];
      for(var k in row){
          var td = document.createElement('td');
          td.innerHTML = `<a href="javascript:;"> ${row[k]} </a>`;
          tr.appendChild(td);
      }
}

var as = document.querySelectorAll('a');
for(var i = 0; i < as.length; i++){
    as[i].onclick = function(){
        tbody.removeChild(this.parentNode.parentNode);  // this-a标签, .parent-td, .parent-tr;
    }
}
```

###### fragment 文档片段

> 每次使用 appendChild, Dom 树会重新绘制, 浏览器都会重新渲染页面。频繁更新 DOM 节点，会非常消耗内存，影响用户体验; 把所有需要构造的节点都放到文档片段中执行，这样可以不影响文档树，也不会造成页面渲染。当节点都构造完成后， 再将文档片段对象 添加到页面中， 一次性渲染出来， 减少浏览器负担，提高渲染速度;

```
var ul = document.querySelector('ul');
var frag = document.createDocumentFragment();

for(var i = 0; i < 3; i++){
	var p = document.createElement('p');
		p.innerHTML = i;
	frag.appendChild(p);
}

ul.appendChild(frag);
```

###### 移除事件

```
elem.onclick = null
// or
elem.removeEventListener("事件名", 原处理函数)
```

###### 事件流 -- 捕获&冒泡

![](/images/Dom&Bom/3.webp)

```
<div class="father">
    <div class="son"></div>
</div>

<script>
var father = document.querySelector('.father');
var son = document.querySelector('.son');

son.addEventListener('click', function(e){
    alert('false/省略 是冒泡');
    e.preventDeafult()                   // 阻止默认事件;
    window.event.returnValue;            // ie 6/7/8阻止默认事件

    e.stopProgagation()                  // 阻止冒泡;
    window.event.cancelBubble = true;    // ie 6/7/8阻止冒泡
}, false);


father.addEventListener('click', function(e){
    alert('true 是捕获');
    console.log(e.type);                     // click 点击事件名字
    var e = e || window.event;               // ie 6/7/8需要(了解)；
    var target = e.target || e.srcElement;   // ie 6/7/8需要(了解)；

}, true);

</script>
```

###### 事件委托/事件代理

```
var ul = document.querySelector('ul');
ul.addEventListener('click', function(e){
	var allNodes = this.children;
	for(var i of allNodes){
		i.style.background = '#fff';
	}
	var currentNode = e.target;
	currentNode.style.background = 'yellow';
})
```

###### 键盘事件对象

```
document.addEventListener('keydown',function(e){
    console.log(e.keyCode);
});
document.addEventListener('keypress',function(e){
    console.log(功能键(ctrl,shift,↑↓↔)无法触发'keypress');
});
document.addEventListener('keyup',function(e){
    console.log('keydown & keyup 无法区分大小写;  keypress可识别');
});
```

###### 鼠标事件

```
// 禁用右键菜单(了解)
document.addEventListener('contextmenu', function(e){
	e.preventDefault();
})

// 禁用选中文字(了解)
document.addEventListener('selectstart', function(e){
	e.preventDefault();
})
```

###### 鼠标事件对象

注意：鼠标返回距离和绑定事件的元素无关

```
document.addEventListener('click', function(event){
	console.log(event.offsetX);     // 鼠标相对于最近元素的偏移距离

	console.log(event.clientX);     // 鼠标相对于浏览器窗口的可视区域（不包括scroll滚出部分）

	console.log(event.pageX);       // 鼠标相对于文档页面的距离（包括scroll滚出部分）

	console.log(event.screenX);     // 相对于电脑屏幕的距离
})
```

###### 元素偏移量 el.offsetLeft / el.offsetTop

###### 获取元素尺寸 el.offsetWidth

width + 左右 padding + 左右 border;
包含了可能会出现滚动条的宽高

###### 获取元素尺寸 el.clientWidth

width + 左右 padding;
只有可视区域，不计算出现的滚动条的宽高
注意：el.clientLeft 获取元素 border 宽度

###### 获取元素尺寸 el.scrollWidth

width + 左右 padding
会包含父元素设置了超出了边界而隐藏的宽度
内容大小没有超出元素大小时，也就是没有滚动条时 = clientWidth

```
获取带有定位的父级元素，否则相对于body元素
console.log(div.offsetParent)    // <body></body>

不管父级有没有定位,返回最近的父级元素
console.log(div.parentNode)
```

![scrollWidth](/images/Dom&Bom/4.webp)

元素偏移量 & style 区别:
![](/images/Dom&Bom/5.webp)

###### example 图片跟随鼠标移动

```
var pic = document.querySelector('img');

document.addEventListener('mousemove', function(e){
	var x = e.pageX;
	var y = e.pageY;

	var width = pic.offsetWidth;
	var height = pic.offsetHeight;

	pic.style.left = x - width/2 + 'px';
	pic.style.top = y - height/2 + 'px';
})
```

###### example 求鼠标点击位置距离 box 元素多有像素

```
<style type="text/css">
	*{
		margin: 0;
		padding: 0;
	}
	div{
		width: 100px;
		height: 100px;
		border: 2px solid orange;
		margin: 100px;
	}
</style>
<script type="text/javascript">

var box = document.querySelector('div');

box.addEventListener('click', function(e){
	var e = e || window.event;

	var x = e.pageX - this.offsetLeft;
	var y = e.pageY - this.offsetTop;

	console.log('x:', x, 'y:', y);
})
</script>
```

![求鼠标点击位置距离box元素多有像素](/images/Dom&Bom/6.webp)

###### example 求出当前元素在页面的偏移量

```
	<style type="text/css">
		*{
			margin: 0;
			padding: 0;
		}
		.grandfather{
			width: 300px;
			height: 300px;
			border: 10px solid #ccc;
			left: 50px;
			position: absolute;
		}
		.father{
			width: 200px;
			height: 200px;
			border: 10px solid green;
			margin-left: 50px;
			position: relative;
		}
		.son{
			width: 100px;
			height: 100px;
			border: 10px solid orange;
			margin-left: 50px;
		}
	</style>
<script type="text/javascript">

var son = document.querySelector('.son');
console.log(son.offsetLeft);
console.log( getElementLeft(son) );

function getElementLeft(eleObj){
	// 当前元素距有定位的父元素的左偏移量
	var currentLeft = eleObj.offsetLeft;

	// 找到当前元素有定位的父元素
	var parent = eleObj.offsetParent;

	// 最外层是/没有定位父级就是基于<body/>, body.offsetParent是null.
	while(parent != null){
		// 当前元素做偏移量 + 父元素的border-left + 父元素的左偏移量
		currentLeft = currentLeft + parent.clientLeft + parent.offsetLeft;
		console.log(parent);
		parent = parent.offsetParent;
	}
	return currentLeft;
}
</script>
```

![](/images/Dom&Bom/7.webp)

###### example 可拖动弹窗

##### scroll 系列

滚动窗口至文档中的特定位置

##### el.scroll()

元素滚动到特定坐标

###### el.scrollTop 滚出内容的距离

区分：el.scrollWidth/scrollHeight 获取元素宽度

![](/images/Dom&Bom/8.webp)
![](/images/Dom&Bom/9.webp)

###### example 滚动到一定距离就固定

```
<script>
var slidebar = document.querySelector('.slide-bar');
var header = document.querySelector('.header');
var goBack = document.querySelector('.go-back');

			  // css设置的距离500px高度 - header的高度100;
var slidebarTop = slidebar.offsetTop - header.offsetHeight;

document.addEventListener('scroll', function(){

	// console.log(window.scrollTop);	// undefined;
	console.log(window.pageYOffset);// 获取document滚出距离需用pageYOffset;

	if(window.pageYOffset >= header.offsetHeight){
		slidebar.style.position = 'fixed';
		slidebar.style.top = slidebarTop + 'px';
		goBack.style.display = 'block';
	}else{
		slidebar.style.position = 'absolute';
		slidebar.style.top = '500px';
		goBack.style.display = 'none';

	}
})

goBack.addEventListener('click',function(){
	window.scrollTo(0,0)
});
</script>

<style type="text/css">
	*{
		margin: 0;
		padding: 0;
	}
	.header{
		width: 80%;
		height: 100px;
		background-color: pink;
		margin: 0 auto;
	}
	.wrapper{
		width: 80%;
		height: 1000px;
		background-color: yellowgreen;
		margin: 0 auto;
	}
	.slide-bar{
		width: 50px;
		height: 100px;
		background-color: orange;
		position: absolute;
		right: 20px;
		top: 500px;
	}
	.go-back{
		display: none;
	}
</style>
```

## ![](/images/Dom&Bom/8.webp)

### BOM

![](/images/Dom&Bom/10.webp)

![](/images/Dom&Bom/11.webp)

###### window.onload

当文档内容(图片, 脚本文件, css, 外部文件)完全加载完会触发该事件

```
	/*
	window.onload = function(){
		var btn = document.querySelector('button');

		btn.addEventListener('click', function(){
			console.log('多个window.onload事件，最后的 window.onload 注册事件会覆盖以前的');
		})
	}
	*/

	window.addEventListener('load',function(){
		var btn = document.querySelector('button');

		btn.addEventListener('click', function(){
			console.log('addEvent方式则不会');
		})
	})

	window.addEventListener('load',function(){
		alert(22)
	})

	document.addEventListener('DOMContentLoaded',function(){
		alert('DOM加载完毕就执行事件')
	})
```

###### window.onresize

窗口大小发生像素变化触发事件

```
var div = document.querySelector('div');

window.onresize = function(){
	// 当前屏幕的宽度;
	if(window.innerWidth <= 800){
		div.style.display = 'none';
	}else{
		div.style.display = 'block';
	}
}
```

###### window.setTimeout & setInterval 定时器

###### window.location

解析/获取/设置访问的 URL 的对象

```
  属性:
       location.href        完整url
       location.protocol    http/https/tcp 协议
       location.host        主机名+端口号: 比如localhost:3000
       location.hostname    主机名: 比如localhost
       location.port        端口号: 3000
       location.pathname    相对路径
       location.hash        #锚点链接地址
       location.search      ?name=tom&password=123查询字符串

  方法:
   location.href="新url"
   location.assign("新url")  也可实现在当前窗口打开，可后退
   location.replace("新url")  实现禁止后退      原理: 用新url替换history中旧的url

  刷新:
   history.go(0) == location.reload()     1. 普通刷新: 优先从浏览器本地缓存中获取资源。缓存中没有或过期，才去服务器下载新的。
   location.reload(true)                  2. 强制刷新: 跳过浏览器缓存，总是从服务器下载最新的资源
```

```
window.addEventListener('hashChange', (e)=>{
	console.log('hash地址改变', e)
	let oldUrl = e.oldURL;
	let newUrl = e.oldURL;
})
```

###### window.navigator

浏览器保存的配置信息对象

```
if((navigator.userAgent.match(/(phone|pad|pod|iPhone|iPod|ios|iPad|Android|Mobile|BlackBerry|IEMobile|MQQBrowser|JUC|Fennec|WOSBrowser|BrowserNG|WebOS|Symbian|Windows Phone)/i))){
	console.log('手机');
}else{
	console.log('pc')
}

属性:
 cookieEnabled: 判断当前浏览器是否启用了cookie。
 plugins: 保存浏览器安装的所有插件信息的集合
 userAgent: 保存浏览器名称和版本号的字符串
 geolocation: 该对象提供用户地理位置信息，通过getCurrentPosition获取;
	latitude 维度
	longitude 经度
	accuracy 经纬度精确度
	altitude 海拔高度
	altitudeAccuracy 海拔高度经度
	heading 设备方向
	speed 速度

 	navigator.geolocation.getCurrentPosition( position => {
		console.log('用户地理信息:', position)
	});
```

###### window.history

保存当前窗口打开后，成功访问过的 url 的历史记录栈的对象

```
history.forward()  ==  history.go(1) 前进一步
history.back()   ==  history.go(-2) 后退两步
history.go(0) 刷新
```
