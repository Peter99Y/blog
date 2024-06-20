---
title: js
---

```
var likeArray = {
	0: 1,
	1: 2,
	length: 2,
};

var arr = Array.from(likeArray);
console.log(arr);       //[1,2]
```

###### 包装类

基本数据类型是没有属性和方法的，只有对象才有属性和方法，js 默认将 string, number, boolean 类型包装为复杂数据类型;
为了方便操作基本数据类型，js 提供了 String, Number, Boolean 包装类;

```
// 生成临时变量，把简单数据类型包装为复杂数据类型;
var temp = new String('tom');

// 赋值给声明的用户声明的变量
var str = temp;

// 销毁临时变量
temp = null;
```

###### Symbol

生成独一无二的基础数据类型的值；
接收一个字符串作为参数，表示对 Symbol 实例的描述；

-   优点：作为对象/常量的唯一属性名，避免被覆盖；
-   缺点：for of / Object.keys 无法给出对象的 Symbol 属性名；

```
let keys = {
	user: Symbol("user"),
	say: Symbol("say"),
};

let obj = {
	[keys.user]: "tom",
	[keys.say]: () => console.log("hello"),
	age: 10,
};

console.log(Symbol('user') == obj[keys.user]);		// false;

let arr1 = Object.keys(obj); 		// ['age']
let arr2 = Reflect.ownKeys(obj); 	// [Symbol(user), Symbol(say), age]
```

```
const VIDEO = Symbol();
const AUDIO = Symbol();

function change(type) {
	switch (type) {
		case VIDEO:
			return 1;
		case AUDIO:
			return 2;
		default:
			return 3;
	}
}
change(VIDEO);
```

###### Symbol.iterator

Array, String, Map, Set, Arguments Nodelist 类型在原型对象上有默认的迭代器属性 Symbol.iterator;
ES6 提供 for...of 命令对数组类型等可以遍历操作，因为在原型对象上执行了 Symbol.iterator 迭代函数，它不断地调用每个数据对象 next 方法指向下一个数据对象；

```
let str = 'hi';
let strIt = str[Symbol.iterator]();

let arr = ["a", "b"];
let it = arr[Symbol.iterator]();  // 获得迭代器对象;

console.log(it.next());           // { value: 'a', done: false }
console.log(it.next());           // { value: 'b', done: false }
console.log(it.next());           // { value: undefined, done: true }
```

---

对象属于非线形，无序的数据结构，无法 for...of 遍历；
对象 for...of 遍历报错：TypeError: obj is not iterable;
给对象/其他的数据结构类型添加 Symbol.itrator 属性就能使其拥有 for...of 被遍历的能力；

```
let obj = {
	[Symbol("user")]: "tom",

	// 不需要用Symbol()包装，默认就能形成 Symbol(Symbol.iterator)属性；
	[Symbol.iterator]: function () {
		console.log("it");
	},
};

let arr = Reflect.ownKeys(obj);     // [Symbol(user), Symbol(Symbol.iterator)]
obj[Symbol.iterator]();             // it
```

```
let obj = {
	0: "a",
	1: "b",
	length: 2,

	[Symbol.iterator]: Array.prototype[Symbol.iterator],
};

for (var i of obj) {
	console.log(i);			// a b
}
```

```
let obj = {
	// list就像是class private私有属性，别人无法直接通过obj.list直接访问；
	// 利用迭代器提供for...of方法返回私有属性的值的数组；
	list: ["a", "b", "c"],

	// 迭代器
	[Symbol.iterator]: function () {
		let index = 0;
		return {
			next: () => {
				return {
					value: this.list[index++],
					done: index === this.list.length + 1,
				};
			},
		};
	},
};

for (var i of obj) {
	console.log(i); // a b c
}
```

###### Set

接收单个/数组数据，并返回对象；

```
const s = new Set();
s.add(1).add(2).add(2);

console.log(s.has(1));      // true
console.log(s);             // {1, 2}
console.log(s.size);        // 2

const arr = [...s];
console.log(arr);           // [1, 2]

s.clear();                  // 清空数据
```

js 去重

```
function unique(arr) {
	var res = [];

	for (var key of arr) {
		if (!res.includes(key)) {
			res.push(key);
		}
	}
	return res;
}
```

###### Array.from

将带有 length 属性的伪数组转成真正的数组，后可调用数组的方法；

```
var lis = document.querySelectorAll("ul li");
var arr = [...lis];

console.log(lis instanceof Array); // false
console.log(arr instanceof Array); // true
```

###### Map

键名可以是任意类型基本类型或复杂类型数据（数字，布尔，函数，对象等）存储，并返回对象；

```
let m = new Map([
	["name", "tom"],
	[{ city: "北京" }, "203321"],
]);
m.set(function () {}, "fn");
m.set(NaN, NaN);

m['age'] = 'tom';
console.log(m.has('age')); // false; 注意不能直接赋值，虽然对象能添加但get,has,size,forEach将获取不到；

console.log(m.get("name"));	// "tom"

console.log(m);
/**
 {
    'name' => 'jack',
    { city: '北京' } => '203321',
    [Function] => 'fn',
    NaN => NaN
}
 */
```

###### Proxy

Object.definedProperty 只能对对象单个属性拦截，且只能是对象；
创建一个对象的代理，从而实现基本操作的拦截，可以是对象，数组，函数等；

```
// let obj = {};
let obj = new Map();

let p = new Proxy(obj, {
	get(targetObj, key) {
		let value = targetObj[key];

		// 如访问的代理对象的方法名，则将原对象上的方法返回；
		if (value instanceof Function) {
			return value.bind(targetObj);
		}

		// 则返回目标对象上的属性
		return value;
	},

	set(targetObj, key, newValue) {
		console.log("proxy set");
		targetObj[key] = newValue;
	},
});

// 普通对象; 对普通对象设置属性，走代理对象set；
// p.user = "tom";

// Map/Set对象; 走代理对象get，执行其返回的原对象的函数，
p.set("user", "tom");                   // 等于Map.set('user');
console.log(p.has("user"));             // true

// Map/Set对象；走代理对象set；但因为不能对Map对象直接赋值，get,has,size,forEach将获取不到，
p.age = 10;
console.log(p.has('age'));              // false; 所以没有；
```

```
let obj = [];

let p = new Proxy(obj, {

	get(targetObj, key) {
		let value = Reflect.get(targetObj, key);
		if (value instanceof Function) {
			return value.bind(targetObj);
		}
		return value;
	},

	set(targetObj, key, newValue) {
		Reflect.set(targetObj, key, newValue);
	},
});

// 数组；先走代理对象get得到push函数，再走set，根据下标设置值；
p.push(2);
```

###### Reflect

与 Object 类似获取目标对象的行为，但操作更易读；

-   1.代替 Object 某些方法；
-   2.修改了某些 Object 方法的返回结果，如 Reflect 失败报错需要 try 捕捉；
-   3.命令式变函数式；
-   4.配合 Proxy 实现拦截；

```
try {
    let obj = {};
	Reflect.defineProperty(obj, "user", {
		value: "tom",
		writable: false,
		enumerable: false,
		configurable: false,
	});

	// 2.修改了某些Object方法的返回结果，如失败报错需要try catch捕捉；
	Object.defineProperty(obj, "user", { value: "jack" });

	// Reflect失败返回 false;
	let res = Reflect.defineProperty(obj, "user", { value: "jack" });


	// 3.命令式变函数式
	console.log("user" in obj);                 // true; 包含原型上属性；
	console.log(Reflect.has(obj, "user"));      // true; 包含原型上属性；

	delete obj.user;
	Reflect.deleteProperty(obj, "user");
} catch (err) {
    console.log(err)
}
```

###### string

```
let str = ' hello '.strim();		// 'hello'
```

```
let hour = '5'.padStart(2, '0');	// '05' 参1填充长度，参2填充字符
```

```
let str = 'abc'.toUpperCase();		// 'ABC'
let str = 'ABC'.toLowerCase();		// 'abc'
```

```
let str = 'abc'.startsWith('A');	// false
let str = 'abc'.endsWith('A');		// false
```

```
let str = 'abc'.charAt(0);		// 'a'	根据下标返回字符
let str = 'abc'.charCodeAt(0);		// 97	根据下标返回ASCII码
```

```
let str = 'abc'.indexOf('d');		// -1	根据字符查询下标
let str = 'abc'.lastIndexOf('d'); 	// -1	根据字符查询最后一次出现的下标
```

```
let str = 'abc'.substr(0,1);		// 'a' 截取字符,不改变原字符
```

```
let str = 'china是美丽的国家'.replace('china', '中国');
let str = 'CHINA是美丽的国家'.replace(/china/gi, '中国');
```

```
let str = 'china is china'.match(/china/ig);		// ['china', 'china']
```

###### number

```
let num =  isNaN (1-'a');		// true

let num =  isFinite(10/3);		// true

let num =  isNaN( Number('1a') );	// true
```

###### parseInt

```
let num = parseInt(1.23);		// 1

let num = parseInt('7a12');		// 7

let num = parseInt('a712');		// NaN
```

###### parseFloat

```
let num = isNaN(parseFloat('a34'));			// true

let num = parseFloat('1.32a3');			// 1.32

let num = parseFloat('11a1').toFixed(2);		// '11.00'
```

###### 隐式转换

> NaN、 ''、 null、 0、 false、 undefined

```
let res = 1 + true;			// 2

let res = true + undefined;		// NaN
let res = 1 + undefined;		// NaN
let res = 'a' + undefined;		// 'aundefined'

let res = true + null;			// 1
let res = 1 + null;			// 1
let res = 'a' + null;			// 'anull'

let res = null == 0;			// false
let res = true == 1;			// true

let res = '3' > '1%';			// true;  两个字符串比较，只比较首个字母的ASCII码

let res = NaN == undefined;		// false; NaN和任何比较都是false;

let res = 10 > '3';			// true
let res = 3 > '10a';			// false; 数字和字符串比较，会隐式转成Number再比较
```
