---
title: js
---

```
// 函数声明 (命名函数)
function fn() {}

// 函数表达式 (匿名函数; 给变量赋值一个函数)
var fun = function () {};
```

### 函数预编译

> 预编译发生在函数执行的前一刻，也就是为什么变量提升和函数声明提升的原理;

预编译 4 步骤：

-   1. 创建 AO 对象(也就是执行期上下文).
-   2. 找形参和变量声明，将形参名和变量名作为 AO 对象的属性名 **(变量声明提升)** ，值为 undefined.
-   3. 将形参和实参值统一.
-   4. 找函数声明作为 AO 对象属性名 **(函数声明提升)** ，并赋值函数体.

```
function fn(a) {
	console.log(a);     // f a (){}
	console.log(b);     // undefined

	var a = 123;
	console.log(a);     // 123

	function a() {}

	var b = function () {};
	console.log(b);     // f (){}
}
fn(1);
```

```
// 1.创建AO对象.
// 2.形参名和变量名作为 AO属性名, 属性值为undefined.
var AO = {
	a: undefined,
	b: undefined
}

// 3.将形参和实参统一赋值.
AO = {
	a: 1,
	b: undefined
}

// 4. 函数a 覆盖掉 原来变量a 的AO属性名，并赋值函数体.
//    var b = function是函数表达式，不是函数声明，所以不赋值undefined.
AO = {
	a: function a(){},
	b: undefined,
}
```

###### example

```
function fn(a, b) {
	console.log(a); // 1
	a = 3;
	b = 2;
	console.log(b);	// 2

	function b(){}	// 注意：这里是函数声明, 不是函数表达式(var b = fn, 不是赋值操作);
	console.log(b);	// 2; 所以还是2，不是f(){};
}
fn(1);
```

### 全局预编译

只是没有函数预编译的第 3 步(没形参)
第一步创建的叫 GO 对象

```
console.log(a);		// f a(){}

var a = 123;
function a(){};

console.log(a);		// 123
```

###### example

```
global = 100;
console.log(global);		// 100; 		访问GO.global;

function fn(){
	console.log(global);	// undefined; 	        访问AO.global;

	global = 200;		// 			GO.global赋值;
	console.log(global);	// 200; 		访问GO

	var global = 300;	// 			AO.global赋值;
	console.log(global);	// 300; 		访问AO
}

fn();
var global;
```

```
var GO = {
	// 1. 生成GO对象, 找变量声明global作为GO属性名:undefined;
	// 接着执行第一行js代码，赋值给global;
	global: 100,

	// 2. 执行fn函数，生成AO对象，找函数内变量声明global:undefiend;
	// 3. 执行fn函数第一行代码，访问的是AO自身的变量;
	AO:{
		global: undefined,
	}
}
```

```
function fn(){
	console.log(b);		// undefined
	if(a){
		var b = 100;	// 别管if条件，预编译同样把它作为AO属性名
	}

	// a = 10是在fn()完后才执行; if(a) 的a还是undefined; 所以AO.b没有赋值100;
	console.log(b);		// undefined;
	c = 234;
	console.log(c);		// 234
}

var a;
fn();
a = 10;
console.log(c); 		// 234
```

```
function bar(){
	return foo;
	foo = 10;
	function foo(){}
	var foo = 11;
}

console.log(bar());	// foo(){}
```

```
var a = 1;
function fn() {
	a = 3;
}
console.log(a);	// 1;   fn没有执行;
```

---

### 作用域

> 执行期上下文: 当函数执行时,会创建一个执行期上下文的内部对象(GO 和 AO 对象).一个执行期上下文定义了一个函数执行时的环境, 函数每次执行时对应的执行上下文都是独一无二的,当函数执行完毕,执行上下文就被销毁;

> 作用域: 每个函数都是一个对象, 对象中有 像 prototype 和隐士[[scope]]属性, [[scope]]属性指得就是作用域，它存储了执行期上下文的数组集合;

> 作用域链: 作用域存储的执行期上下文对象的数组集合, 这个集合呈链式链接,出现作用域链后, 访问变量时的顺序是根据作用域[[scope]]从下标 0 位开始寻找;

js 中只有两级作用域 window ，function (for 循环体, if 语句不是局部作用域哦);

全局作用域：window.
局部作用域：函数内部就是局部作用域.

全局变量：属于 window 对象的属性，不能访问局部变量，浏览器关闭才会销毁(占内存);
局部变量：可以访问全局变量，局部变量不能访问另外局部变量，函数执行完毕就会销毁(节约内存);

```
var a = 1;                  // 全局变量 => window.a;

function demo(arg) {
	b = 2;              // 全局变量(未声明变量属于window.b);

	var c = 3;          // 局部变量
	console.log(arg);   // 局部变量(形参也属于局部变量)

        var d = k = 5;      // k未声明属于全局变量, d局部变量
}
demo(4);
```

块级作用域：es6 新增，使用 let 声明所处的{}变成块级作用域，只在花括号内可以访问;

```
if (1) {
	var num = 10;
	let count = 20;
	console.log(count);     // 20
}
console.log(num);               // 10;
console.log(count);             // undefined 块级外部

for (var i = 0; i < 1; i++) {}
for (let k = 0; k < 1; k++) {
	console.log(k);         // 0
}

console.log(i);                 // 1
console.log(k);                 // undefined  块级外部
```

---

### 闭包

当内部函数被保存到外部时，就会生成闭包。
弊端：闭包会导致原有的作用域链不被释放，造成内存泄漏;

```
function fn() {
	function b() {
		// 为什么不是undefined? 在fn内部没有执行b函数;
		// f()执行时fn函数内部全部执行完了，也就给a赋值了1;
		console.log(a); // 1
	}
	var a = 1;
	return b;
}

var f = fn();
f();
```

-   闭包作用：实现共有变量 (函数累加器)

```
function add() {
	var count = 0;
	return function () {
		count++;
		console.log(count)
	};
}

var counter = add();
counter();  // 1
counter();  // 2
counter();  // 3
```

-   闭包作用：模块化开发，防止污染全局

```
var f = (function rideTotal() {
    var bill = 10;
    var total = 0;

    return {
        calcMile: function (mile) {
            if (mile <= 3) {
                total = bill;
            } else {
                total = bill + (mile - 3) * 1;
            }
            return total;
        },
        // 扩展其他方法...
    };
})();

console.log("前3公里10元，后每公里1元。8公里乘车金额：", f.calcMile(8));
```

-   闭包作用：封装，属性私有化(...)

###### 闭包 & 立即执行函数:

给元素绑定函数并不是马上执行，是调用时才执行。而 for 循环已经结束了，所以每个元素最后绑定的都是 4;
把实参 i 传递给立即执行函数，时间函数内访问着立即执行函数的形参，形成了闭包;

```
var lis = document.querySelectorAll('li');

// 需求：想要访问索引号i;
for(var i = 0; i < lis.length; i++){
    // lis[i].index = i;
    // lis[i].onclick = function(){
    //     console.log(this.index);
    // }

    // 给元素绑定函数并不是马上执行，是调用时才执行。而for循环已经结束了，所以每个元素最后绑定的都是4;
    // 把实参i传递给立即执行函数，事件函数内访问着立即执行函数的形参没有释放，形成了闭包;
    // 立即执行函数不是会销毁吗？不是，具体说的引用指向销毁，而绑定内访问的值还是握着的;
    (function(index){
        lis[index].onclick = function(){
            console.log(index);
        }
    })(i);
}
```

```
var lis = document.querySelectorAll("li");

// 需求：点击li打印里面的内容(实际问题是解决for循环内执行异步)
for (var i = 0; i < lis.length; i++) {
	(function (index) {
		lis[index].onclick = function () {
			setTimeout(() => {
				console.log(lis[index].innerHTML);
			}, 1000);
		};
	})(i);
}
```
