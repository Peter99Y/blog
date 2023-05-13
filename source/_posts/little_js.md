---
title: little js
---

#### new Set()

Set 构造函数去重

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

#### 伪数组转数组

转成真正的数组后，可调用数组的方法

```
var lis = document.querySelectorAll("ul li");
var arr = [...lis];

console.log(lis instanceof Array); // false
console.log(arr instanceof Array); // true
```

```
var likeArray = {
	0: 1,
	1: 2,
	length: 2,
};

var arr = Array.from(likeArray);
console.log(arr);       //[1,2]
```

#### 包装类

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

#### string

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
let str = 'china是一个美丽的国家'.replace('china', '中国');
let str = 'CHINA是一个美丽的国家, 啊，china!'.replace(/china/gi, '中国');
```

```
let str = 'china is china'.match(/china/ig);		// ['china', 'china']
```

#### number

```
let num =  isNaN (1-'a');		// true

let num =  isFinite(10/3);		// true

let num =  isNaN( Number('1a') );	// true
```

```
let num = parseInt(1.23);		// 1

let num = parseInt('7a12');		// 7

let num = parseInt('a712');		// NaN
```

```
let num = isNaN(parseFloat('a34'));		// true

let num = parseFloat('1.32a3');			//1.32

let num = parseFloat('11a1').toFixed(2);	// '11.00'
```

### 隐式转换

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
