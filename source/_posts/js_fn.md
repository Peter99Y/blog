---
title: Function
---

## 函数

> 所有函数都是构造函数 new Function() 的实例对象;

```
// 函数声明 (命名函数)
function fn() {}

// 函数表达式 (匿名函数)
var fun = function () {};

console.log(f instanceof Object);                   // true
console.log(f.__proto__ === Function.prototype);    // true
```

-   函数内部的 this 指向

```
// 普通函数
function fn() {
    console.log(this)       // window
}
fn();

// 对象方法
var obj = {
    say:function(){
        console.log(this);  // obj
    }
}
obj.say();

// 构造函数
function Start() {
    console.log(this);      // 实例对象
}
new Start();

// 事件函数(是异步函数哦)
btn.onclick = function(){
    console.log(this);      // btn元素
}

// 定时器函数
setInterval(()=>{
    console.log(this);      // window
});

// 立即执行函数
(function(){
    console.log(this);      // window
})()
```

###### 函数方法 call, apply, bind 改变 this 指向

-   call 调用函数并改变 this，主要作用于实现继承

```
function fn(a, b) {
	console.log(this);        // obj
	console.log(a+b);         // 3
}

let obj = { name: "c" };

fn.call(obj, 1, 2);
```

```
function Product(name, price) {
    this.name = name;
    this.price = price;
}

function Food(name, price, category) {
    Product.call(this, name, price);
    this.category = category;
}

let food = new Food("cheese", 5, "food");
```

-   apply 调用函数并改变 this，把数组解析成单数

```
function fn(val, val2, val3) {
	console.log(this);            // obj
	console.log(val,val2,val3);   // 1 2 3
}

let obj = { name: "c" };

fn.apply(obj, [1, 2, 3]);
```

```
// Math.max(1,2)只接受单个参数, 不接受数组; 利用apply把数组解析成单数
let max = Math.max.apply(null, [1,2,3]); // = Math.max(1,2,3);
console.log("max:", max);
```

-   bind 不调用函数，绑定并返回新的函数

```
function fn(a, b) {
	console.log(this);
	console.log(a + b || false);
}

var obj = { name: "c" };

var f = fn.bind(obj, 1, 2);
f();            // obj;     3
fn();           // window;  false
```

```
var btn = document.querySelector("button");
btn.onclick = function () {
	this.disabled = true;
    let that = this;
	setTimeout(
		function () {
            // that.disabled = false;
			this.disabled = false;
		}.bind(this),
		3000
	);
};
```

## 回调函数

作为参数的函数称为回调函数，这个函数在异步函数后执行;

回调函数、立即执行函数、Promise 的 async 和 await 都能解决异步问题;

```
function todo(str) {
	console.log(str);
}
todo("买鸡");
todo("炖鸡");
todo("吃鸡");
```

```
function delayTodo(str, callback) {
	setTimeout(() => {
		console.log(str);

		let res = null;
		if (str === "炖鸡") {
			res = "炖好了";
		} else {
			res = "没炖好";
		}

		callback(res);
	}, 1000);
}

todo("买鸡");

delayTodo("炖鸡", (data) => {
	if (data === "炖好了") {
		console.log("吃鸡");
	}
});
```

## 函数预编译

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

## 全局预编译

第一步创建的叫 GO 对象
没有函数预编译的第 3 步(没形参)

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
		var b = 100;	// 别管if条件，预编译同样把它作为AO属性名;
	}

	// a = 10是在fn()完后才执行; if(a) 的a还是undefined; 所以AO.b没有赋值100;
	console.log(b);		// undefined;
	c = 234;
	console.log(c);		// 234	Go.c;
}

var a;
fn();
a = 10;
console.log(c); 		// 234	Go.c;
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

## 作用域

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

## 闭包

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

## 构造函数

-   创建对象三种方式：

    1.  var obj = { }; 对象字面量 (特殊键名必加引号)
    2.  var obj= new Object();
    3.  构造函数;

> 构造函数一种特殊的函数, 用来初始化对象, 即为对象成员变量赋初始值, 与 new 一起使用, 我们可以把对象中一些公共的属性和方法抽取出来, 然后封装到这个函数里面;

-   new 一个新的对象执行的步骤：

    1.创建新对象{ }; 2.赋值给 this, 将 this 指向这个对象; 3.给这个新对象添加属性和方法;
    4.return 新对象;

```
function Animal(name) {
    // 实例成员: 在构造函数内部通过this添加的属性or方法, 只能由实例化对象来访问.
	this.name = name;
	this.run = function (method) {
		console.log(this.name, "is running", method);
	};
}

// 静态成员: 在构造函数本身上添加的属性or方法, 只能由构造函数本身访问.
Animal.bark = () => {
	console.log("roar loudly");
};

var dog = new Animal("dog");
var cat = new Animal('cat');

dog.name;           // dog
dog.run("fast");    // dog is running fast
Animal.bark();      // roar loudly
```

###### prototype 原型对象

-   构造函数的缺陷: 每 new 一个实例对象, 里面的方法(函数是引用类型)就会开辟一块内存(如果有一百个实例对象占据一百个空间，但却存储相同的属性和函数，浪费内存);

-   在构造函数的原型对象中(如 Animal.prototype)添加公共的属性 or 方法;
    实例对象.\_\_proto\_\_指向了原型对象，就能使用原型对象中的成员;

-   原型对象中 this & 构造函数中 this 指向实例对象;

```
Animal.prototype.bite = function(){
    console.log('chips');
}

console.log(dog.run === cat.run)    // false   构造函数里的方法
console.log(dog.bite === cat.bite)  // true    原型对象上的方法
console.log(dog.__proto__ === Animal.prototype);    // true
```

###### constructor

是构造函数的原型对象中的属性，称为构造函数, 此属性指向了此对象引用于哪个构造函数;

```
// Animal.prototype.bite = function(){
//    console.log('chips');
// }

Animal.prototype = {
    // 当用对象的形式赋值给原型对象时，指向的对象没有constructor指向构造函数;
    constructor: Animal,

    bite: function(){
        console.log('chips');
    },
    drink: function(){
        console.log('beer')
    }
}
```

![构造函数和prototype和__proto__关系图](/images/js/prototype.png)

##### 原型链

实例对象.\_\_proto\_\_ 指向构造函数的原型对象，
而构造函数的原型对象.\_\_proto\_\_ 指向了 Object 构造函数的原型对象，
Object 构造函数的原型对象.\_\_proto\_\_ 指向 null;

当访问对象的属性和方法时(就近原则), 首先查找对象自身是否有，如没有，就会根据原型链向上查找，直到最后 undefined;

![](/images/js/proto.png)

##### 继承

ES6 没有 extends 继承，而是利用函数继承父类型的属性，利用原型对象继承父类型的方法;

```
function Product(name, price) {
	this.name = name;
	this.price = price;
}

function Food(name, price, category) {
	Product.call(this, name, price);
	this.category = category;
}

let food = new Food("cheese", 5, "food");
```

###### 构造函数模式

每个 new 出来的实例对象都是独立的，但里面的属性和方法重复，造成内存泄漏；

```
function Animal(name){
	this.name = name;
	this.bark = function(){
		console.log('wow!')
	}
}

var cat = new Animal('cat');
var dog = new Animal('dog');

console.log(cat.__proto__ === dog.__proto__);	// false;
```

###### 原型链模式

弊端：继承的原型对象的属性和方法同时会被修改；

```
function Animal(){
	Animal.prototype.category = '动物';
}

function Dog(){
	Dog.prototype = Father.prototype;
	Dog.prototype.name = 'dog';
}

var dog = new Dog();
var animal = new Animal();

console.log(animal.name);	// 'dog'
console.log(dog.name);		// 'dog'
```

###### 圣杯模式

```
Product.prototype.sum = function (arr) {
	return arr.reduce((prevValue, currentValue) => prevValue + currentValue, 0);
};

function Product(name, price) {
	this.name = name;
	this.price = price;
}

Food.prototype = new Product();
Food.prototype.constructor = Food;

Food.prototype.minus = function(a, b){
    return a - b;
}

function Food(name, price, category) {
	Product.call(this, name, price);
	this.category = category;
}

let food = new Food("cheese", 5, "food");
console.log(food.name, food.price, food.category);  // 继承属性
console.log(food.sum([1, 2, 3]));                   // 继承方法
console.log(food.minus(5,3));                       // Food构造函数独有方法
```

分析：
son 实例对象 .\_\_proto\_\_ 查找 father 实例对象,
father 实例对象.\_\_proto\_\_ 查找到 father.prototype 原型原型对象上的方法，就能调用了.

---

## 类

是一个抽象性的模板，对于该类事物的综合描述，这些描述中分为两种内容，一是属性，二是方法；

实例化: 使用 new 根据类创造出一个实例对象；

consturctor：**是类的构造函数，用于传递参数，返回实例对象**，通过 new 命令生成对象实例时，自动调用该方法。如没有定义，类内部自动创建一个 constructor;

> 注意： 1.类没有声明提升； 2.类中的方法都默认开启了严格模式

```
// 创建类
class Animal {
	constructor(name) {
		// 实例对象共有属性
		this.name = name;
		this.init();         // 创建实例对象就立即调用;
	}
	init(){
		console.log('initial finished');
	}
	// 实例对象共有方法
	run(method) {
		console.log(this.name, `is runing ${method}`);
	},
	// 静态方法
	static eat(){
		console.log('chips');
	}
}

// 生成实例对象
var dog = new Animal("dog");
var cat = new Animal("cat");

console.log(dog.name); // dog
console.log(cat.name); // cat
dog.run("fast");
cat.run("slow");
```

等价于 ES5 构造函数

```
function Animal(name) {
	this.name = name;
}
// 静态方法
Animal.eat = function () {
	console.log("chips");
};
// 原型方法
Animal.prototype = {
	constructor: Animal,
	run(method) {
		console.log(this.name, " is running", method);
	},
};

var dog = new Animal("dog");
Animal.eat();
dog.run("fast");
```

---

##### extends & super

-   子类可以继承父类的方法和属性；
-   继承后遵循就近原则，最后调用**父类普通函数**；
-   super 关键字用于访问和调用父类上的函数。可以调用父类的构造函数和普通函数；

```
class Father {
	run(){
		console.log('run')
	}
	say() {
		console.log('f')
	}
}

class Son extends Father{
	say(){
	    console.log('s')
	}
}

var s = new Son(1, 2);
s.run();    // run
s.say();    // s
```

```
class Father {
	constructor(x, y) {
		this.x = x;
		this.y = y;
	}
	sum() {
	    console.log(this.x + this.y);
	}
	say() {
		console.log("fuck");
	}
}

class Son extends Father{
	constructor(x, y){
		// 调用父类constructor构造函数; 等价于 Father.constructor(x,y);
		super(x, y);
	}
	say() {
		super.say();        // 调用父类普通函数;
	}

}

var s = new Son(1, 2);
s.sum();           // 3
s.say();           // fuck
```

分析：

1. new Son(1,2)传给的是 son.contrutor(x,y);
2. s.sum()调用，是 father.sum 函数, 而 sum 函数里的 this.x 指是 father.constructor.x; 所以没访问到就报错；
3. 利用 super(x,y)调用父类构造函数 等价于调用 father.constructor(x,y), 并传递形参;
4. 最后 s.sum()调用，sum(){}函数中 this.x 就能访问到 father 自身上的 x 属性了；

---

```
class Father {
	constructor(x, y) {
		this.x = x;
		this.y = y;
	}
	sum() {
		console.log(this.x + this.y);
	}
}

class Son extends Father {
	constructor(x, y) {
		super(x, y); // 必须在this前调用
		this.a = x;
		this.b = y;
	}
	minus() {
		console.log(this.a - this.b);
	}
}

var s = new Son(3, 2);
s.sum();                 // 5
s.minus();               // 1
```

###### 注意：

```
let that;

class Animal {
	constructor(name) {
		this.name = name;
        that = this;

		this.btn = document.querySelector("button");
		// btn元素点击调用执行，run函数this指向的是btn元素本身;
        // 利用一个全局变量，将实例对象赋值其中，这样不管谁调用run函数，that都指向的是实例对象；
		this.btn.onclick = this.run;
	}
	run() {
		console.log(this);
        console.log(that.name);
	}
}

var dog = new Animal("dog");
dog.run();     // 这里dog.run调用，run函数里的this, 指向dog实例对象;
```

##### example

```
// let that;
class Tab {
	constructor(id) {
		that = this;
		this.el = document.querySelector(id);
		this.addBtn = document.querySelector("button");
		this.ul = this.el.querySelector("ul");
		this.tabContent = this.el.querySelector(".tab-content");
		this.init();
	}
	init() {
		this.getNode();
		this.addBtn.onclick = this.addTab.bind(this.addBtn, this);
		for (var i = 0; i < this.lis.length; i++) {
			// 给每个li标签添加属性index;
			this.lis[i].index = i;
            // bind(null, this)在严格模式中不可以，不改变switchTab函数的this指向;
            this.lis[i].onclick = this.switchTab.bind(this.lis[i], this);
			this.labels[i].ondblclick = this.editTab;
			this.deleteIcon[i].onclick = this.removeTab.bind(this.deleteIcon[i], this);
		}
	}
	getNode() {
		this.lis = this.el.querySelectorAll("li");
		this.labels = this.el.querySelectorAll(".label");
		this.deleteIcon = this.el.querySelectorAll(".close");
		this.sections = this.el.querySelectorAll("section");
	}
	switchTab(that) {
		that.clearClass();
		this.className = "active";
		that.sections[this.index].className = "active";
	}
	clearClass() {
		// 这里的this是that.clearClass调用，也就是实例对象下的this;
		for (var i = 0; i < this.lis.length; i++) {
			this.lis[i].className = "";
			this.sections[i].className = "";
		}
	}
	addTab(that) {
		that.clearClass();
		// 另外一种方式: createElement, and then innerHTML赋值, appendChild;
		// 此方法可以直接把 字符串格式元素 添加到父元素中;
		var li = `<li class='active'>new tab<span>x</span></li>`;
		that.ul.insertAdjacentHTML("beforeend", li);

		var section = `<section class='active'>new tab的内容</section>`;
		that.tabContent.insertAdjacentHTML("beforeend", section);
		that.init();
	}
	removeTab(that, e) {
		// 阻止冒泡到li
		e.stopPropagation();
		var index = this.parentNode.index;
		console.log("index:", index);
		// ele.remove，把对象从所属的DOM树中删除;
		that.lis[index].remove();
		that.sections[index].remove();
		that.init();

		// 移除元素后，如li中仍然有选中状态的li，就不执行移动到前一个；
		if (document.querySelector("li.active")) return;

		// 移除元素后，移除的元素的是选中状态li，就将选中状态移到前一个；
		index--;
		that.lis[index] && that.lis[index].click();
	}
	editTab() {
		var str = this.innerHTML;
		this.innerHTML = `<input type="text">`;
		// 获取input元素;
		var input = this.children[0];
		input.value = str;
		// 选定状态;
		input.select();
		input.onblur = function () {
			// this是input触发调用;
			this.parentNode.innerHTML = this.value;
		};
		input.onkeyup = function (e) {
			e.keyCode === 13 && this.blur();
		};
	}
}

new Tab("#tab");
```

![](/images/js/tabs.png);
