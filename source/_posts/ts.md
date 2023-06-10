---
title: Ts
---

-   html 页面中引入 ts 文件，需要通过命令 tsc 文件名进行编译成 js 文件，最后引入编译后的 js 文件。

-   vscode 自动编译 ts
    -   1.在所处目录中 tsc --init 生成 tsconfig.json 文件
    -   2.终端 -> 运行任务 -> 监视 tsconfig.json

---

##### 基础类型

undefined & null 都可以作为其他类型的子类型(可以赋值给其他类型的变量)

```
let flag: boolean = true;
flag = null;

let count: number = 10;
count = undefined;

let str: string = "小明";

let nu: null = null;
let un: undefined = undefined;
```

-   数组类型 & 元祖类型
    元祖类型在定义时，数组数据的类型位置/个数都限制了

```
// 数组类型
let arr1: number[] = [1, 2, 3];
let arr2: Array<number | string | boolean> = [1, "2", false, 4];

// 元祖类型
let arr3: [string, number, boolean] = ["1", 2, false];
```

-   any 类型

```
// 不清楚数据类型，不需要进行类型检测
let str: any = false;
let arr: any[] = [1,'2',true];

// 缺点：编译时没有了类型检测
console.log(arr[0].split(''))
```

-   枚举类型
    里面每个数据都可以叫元素，每个元素都有自己的编号，编号从 0 递增，可手动赋值；

```
// 枚举类型
enum Color {
	red,
	green,
	blue,
}

console.log(Color[0]); // red
console.log(Color.red, Color.green, Color.blue); // 访问的是编号 0, 1, 2

let c: Color = Color.red;
console.log(c)        // 赋值的是编号  0
```

-   void 类型
    代表函数没有任何返回值

```
function show():void{
	console.log('run')
}
```

-   object 类型
    函数的参数是 object 类型(第二个 object 定义返回值类型)

```
function getInfo(params: object | string | number): object {
	return {
		name: "tom",
	};
}
console.log(getInfo({ id: 1000 }));     // {name: 'tom'}
```

-   类型推断

```
let info;                  // any类型
info = {name: 'tom'};

let count = 12;            // number类型
count = 'a';		       // error
```

-   类型断言
    -   1.用户主动判定了变量是某种类型
    -   2.两种书写方式

```
function getCount(str: string | number) {
	// if(str.length){
	// 	return str.length;
	// }

	if ((<string>str).length) {        // 断定str是string类型, str.length就不会报错
		return (str as string).length;
	} else {
		return str.toString().length;
	}
}

```

---

##### interface 接口

接口是是一种类型/规范/约束；

-   对象接口

```
interface IPerson {
	// 只读
	readonly id: number;
	name: string;
	age: number;
	// 可有可无
	sex?: string;
}

// 定义一个对象，该对象的类型就是接口IPerson
const person: IPerson = {
	id: 1,
	name: "tom",
	age: 18,
	sex: "male",
};
```

-   函数接口
    接口可以定义函数的参数和返回的类型

```
interface ISearch {
	(age: number, name: string): boolean;
}

const handleSearch: ISearch = function (age: number, name: string): boolean {
	return 1 > 0;
};

console.log(handleSearch(10, "tom"));
```

-   类的接口

```
interface ISay {
	firstName: string;
	lastName: string;
	say();
	greet();
}
interface IRun {
	run();
}

// 接口可继承其他接口
// interface IMix extends ISay, IRun {}

// 定义一个类,这个类的约束就是定义的接口
class Person implements ISay, IRun {
	firstName: string;
	lastName: string;

	constructor(firtname: string, lastname: string) {
		this.firstName = firtname;
		this.lastName = lastname;
	}
	say() {
		console.log("我是个坏蛋");
	}
	greet() {
		console.log("hello earthlings");
	}
	run() {
		console.log("我可以跑10公里");
	}
}

const p = new Person("tom", "k");
console.log(p.firstName, p.lastName);
p.say();
p.greet();
p.run();
```

---

##### 类
