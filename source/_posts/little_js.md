---
title: js 小知识点总结笔记
---

#### new Set()

Set构造函数去重
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
js去重
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