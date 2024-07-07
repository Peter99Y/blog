---
title: Ts
---

### 编译环境

-   tsc --version: 查看版本，检测是否安装成功；

-   全局安装
    npm install typescript -g
-   局部安装
    限定某个项目的 ts 为特定版本。若全局 ts 不同电脑安装不同版本，从而导致某个特性被废除
    npm install typescript -D

-   html 页面中引入 ts 文件，需要通过命令 tsc 文件名（Type Script Compile）编译成 js 文件，最后引入编译后的 js 文件。

-   编译 ts 文件：tsc demo.ts

-   自动编译 ts 文件：tsc demo.ts -w (不会走 tsconfig.json 配置文件，只是编译并监测某个文件)
    <!-- -   在所处目录中 tsc --init 生成 tsconfig.json 文件;
    -   tsc 文件名 -w (监视文件变动); -->
-   生成 tsconfig.json 配置文件： tsc --init

---

### 类型推断

没有设置类型时，默认会推断类型

```
let any; 				// any: any

let isShown = 1 > 2;			// isShown: boolean
isShown = true;

let greet = ["hello", 99];		// greet:(string|number)[]
```

### 联合类型

```
const arr: boolean | number[] = true; 	// 要么是布尔类型，要么是数值数组类型

let fn = function (val: number | string): number | string {
	return val;
};
```

### 交叉类型

```
interface userInt {
	name: string;
}

interface infoInt {
	age: number;
}

const usr = (user: userInt & infoInt): void => {
	console.log(user);
};

usr({ name: "Tom", age: 1 });
```

### js 内置对象

```
let num: Number = new Number(1);
let date: Date = new Date();
let str: String = new String();
let reg: RegExp = new RegExp(/\d/);
let err: Error = new Error("error");
let xhr: XMLHttpRequest = new XMLHttpRequest();

// Promise比较特殊，它接收一个返回类型；
let prm: Promise<string> = new Promise((r) => r("hi"));
prm.then((res) => {
	console.log(res.length); 	// 2
});
```

```
let d1: HTMLDivElement = document.querySelector("div")!; //  div: HTMLDivElement | null

let d2 = document.querySelector("section/header/footer"); // sct: HTMLElement | null

let d3 = document.querySelector(".demo"); // test: Element | null

let d4 = document.querySelector(".demo") as Element; // test: Element

let d5: NodeList = document.querySelectorAll("li");

let d6: NodeListOf<HTMLDivElement | HTMLElement> =
	document.querySelectorAll("div");
```

### never 类型

什么值都没有

```
function run():never{
	throw new Error('错误');
}

run();
```

### void 类型

常用于函数返回值；此类型的值只能是 null 或 undefined;

```
let str:void = null;			// 非严格模式可赋值为null;
str = undefined;

function show():void{
	return null;			// err; 不能将类型“null”分配给类型“void”
}

function run():void | string{
	return 'faster';
}

let sum = (a: number, b: number): string => {
	return `a+b=${a + b}`;
};
let res: string = sum(1, 2);
```

### undefined & null 类型

undefined & null 可以被赋值给其他类型

```
let nu: null = null;
let und: undefined = undefined;	  // 非严格模式下，null和undefined可互相赋值

let flag: boolean = true;
flag = null;

let count: number = 10;
count = undefined;
```

### any 类型 & unknown 类型

```
1. any & unknown 是顶级类型，可以被赋值以下所有类型
2. Object
3. Number & String & Boolean
4. number & string & boolean
5. 1	&	'hello' & true
6. never
```

any 不清楚变量是什么数据类型 or 很多类型都可以使用不限定类型，不需要进行类型校验（但也就失去了 ts 的意义了）

```
let any : any;
any = 'hello';
any = {};
any = Array(1,2,3);

let arr: any[] = [{}, [], 1, false, "a"];
let arr: Array<any> = [];	// 泛型方式

// 缺点：编译时没有了类型检测
console.log(arr[0].split(''))	// err
```

unknown 不清楚什么类型，但是有类型的；

```
let unk: unknown;
let any: any;
unk = unknown;
unk = any;
unk = [1,2,3];
unk = 'hello';

let word2: string = unk; 		// err；把一个unknown类型赋值给string类型就会报错

let word3: string = unk as string; 	// 断言这个类型是string类型
```

区分：

```
let obj: any = { isShown: true, open: () => 123 };
obj.isShown; //  可以读取，但没有提示了
obj.open();

let unk: unknown = { isShown: true, open: () => 123 };
unk.isShown; // err；“unk”的类型为“未知”；unknown类型无法读属性和方法
unk.open();
```

### 数组类型

与元祖相比，不限制长度和位置，只限制里面的类型

```
const arr: string[];					// 字符串数组

const arr: number[] = [1, 2, 3];			// 数值数组

const arr: boolean | number[] = true; 			// 要么是布尔类型，要么是数值数组类型

const arr: (string | boolean)[] = ['hello', true];	// 数组里可包含 字符串和布尔值

let arr: { id: number }[] = [{ id: 1 }];			// 对象数组
arr.push(1);							// err

let arr: (string[] | boolean)[] = [["hello"], true];		// 二维数组
```

###### 数组 & 泛型

```
let arr: Array<number | string | object> = [1, "2", {}];

let arr: Array<Array<{ id: number }> | boolean> = [[{ id: 1 }], true];
```

###### 数组 & interface

```
enum SexType {
	Girl,
	Boy
}

interface UserInter {
	name: string;
	sex: SexType
}

let user1: UserInter = {
	name: "tom",
	sex: SexType.Boy
};

const userArr: UserInter[] = [user1, {name: 'Jack', sex: SexType.Boy}];
```

### 元祖类型

元祖类型在定义时，限制数组数据的类型/位置/长度;

```
let arr: [string, number, boolean] = ["1", 2, false];
arr[0] = 99				// err; 可改值，不能更改类型

let arr: readonly [x: number, y?: boolean] = [1, true];
```

### 对象类型

###### object 类型

```
let obj: object = 1;		// err; 不能将类型“number”分配给类型“object”

let obj: object = [];		// 约束只能是引用类型；
let obj: object = () => 123;

let obj: object = {
  name: "Tom",
  age: 10,
}
obj.city = 'Beijing'		// err; 类型“object”上不存在属性“city”
```

###### 字面量类型

```
let obj: {} = { age: 1 };	// 赋值无效
obj.age = 2;		// err; 类型“{}”上不存在属性“age”


let obj: {			// 约束obj为对象类型，同时约束只能多少个属性，以及是哪些属性， 并且约束属性值类型
	name: string;
	age: number;
	hobbit?: string; 	// 有or没有属性都可
};

obj = {				// err；赋值时缺少age属性
	name: 'tom',
	test: true,		// err；obj字面量没有设置test属性
}
```

###### 对象 & interface

```
/**
let obj: {
	name: string;
	show?(): string;
	[key: string]: any;		// 索引签名；约束key属性名称为string类型，约束属性值为any类型；
	// [key: string]: number;	// 如number则会要求对象所有属性值都是number类型
} = {
	name: "tom",
	age: 10,
	show() {
		return "hello";
	},
	city: "Beijing",
};
*/


interface UserInter {
	name?: string;
	readonly show(): string;
	[key: string]: any;
}

let obj: UserInter = {
	name: "tom",
	show() {
		return "hello";
	},
	city: "Beijing"
};
```

### 类型转换

值没有问题的，转换类型

```
let str: string = "99";

let a: number = str as number; 		// err; string不能直接转成number类型

let b: number = str as unknown as number;   	// 但可以先转成未知类型，再把未知类型转成number类型
console.log(b, typeof b);   			// 99 string
```

```
let fn = (a: number, b: number): number => { return a + b };

let res: string = fn(1, 2) as unknown as string;
console.log(res, typeof res);			// 3	number
```

### 枚举类型

```
let gender: "male" | "female"; 	// gender: "male" | "female"
gender = "man";				// err

let user: gender = 'male';
```

里面每个数据都可以叫元素，每个元素都有自己的编号，编号从 0 递增，可手动赋值；

```
enum Color {
	red,
	green,
	yellow = 5,
	orange,
}

console.log(
	Color[0] 	// red
);
console.log(
	Color[5] 	// yellow
);

console.log(
	Color.red, 	// 0
	Color.green 	// 1
);

let c: Color = Color.orange;
console.log(c); 	// 6
```

```
enum Color {
	red = "red",
	green = "green",
}

console.log(Color.green)  // green
```

###### 反向映射

枚举限数字类型，字符串报错；

```
enum Color {
	yes,
	no,
	fail = 'err'		// 无法反向映射
}

let value = Color.yes;
let key = Color[value];

console.log(key);   		// yes
console.log(value)  		// 0
```

###### 枚举 & interface

```
enum Sex {
	male = "男",
	female = "女",
}

interface userInt {
	name: string;
	habit: Sex;
}

let user: userInt = {
	name: "Tom",
	habit: Sex.male,
};
```

### 函数

```
let fn: Function;			// 约束变量fn必须是函数类型
fn = (a: number, b: number) => {return a };


let sum: (a: number) => number;	// 约束变量sum必须是函数类型、a形参是number类型、返回值是number类型
sum = (x, y) => {			// 赋值函数时，形参名可不一致
	return x;
};
sum(1, 2);
```

###### 函数 & interface

```
interface fnInter {
	(price: number): number;
}

let handleTotal: fnInter = (price: number) => price * 2;
```

```
interface UserInter {
	name: string;
	age: number;
	isLocked: boolean;
}

// interface约束形参 和 返回值
function handleLock(user:UserInter, lock: boolean): UserInter{
	user.isLocked = lock;
	return user;
}

handleLock(user, true);
```

-   定义 this 类型

```
interface ObjInter {
	user: string[];
	add: (this: ObjInter, name: string) => void;
}

let obj: ObjInter = {
	user: ["tom"],
	add: function (this: ObjInter, name: string) {
		this.user.push(name);
	},
};

obj.add("jack");
```

-   函数重载 一个函数通过参数实现多个方法

```
let arr: number[] = [1, 2, 3];

function findNum(): number[]; // 如没传就是查询全部

function findNum(id: number): number[]; // 传id就是单个查询

function findNum(add: number[]): number[]; // 传数值数组就是添加

// 通过区分参数来实现不同功能
function findNum(ids?: number | number[]): number[] {
	if (typeof ids === "number") {
		return arr.filter((i) => i === ids);
	} else if (Array.isArray(ids)) {
		arr.push(...ids);
		return arr;
	} else {
		return arr;
	}
}

console.log(findNum()); // [1,2,3]
console.log(findNum(1)); // [1]
console.log(findNum([4, 5])); // [1,2,3,4,5]
```

###### 函数 & 泛型

```
function add<T, K = boolean>(a:T, b: K):Array<T | K> {
	return [a, b]
}

add(1, true)
```

```
const axios = {
	get<T>(url: string): Promise<T> {

		return new Promise((resolve) => {
			let xhr: XMLHttpRequest = new XMLHttpRequest();
			xhr.open("GET", url);
			xhr.onreadystatechange = () => {
				if (xhr.readyState === 4 && xhr.status === 200) {
					resolve(JSON.parse(xhr.responseText));
				}
			};
		});

	},
};

interface resultData {
	code: number;
	message: string;
}

axios.get<resultData>("...").then((res) => {});
```

### 函数原型

函数原型声明的参数名称只是占位，可不一致；

```
let fn: (a: number, b: number) => number;

fn = (x: number, y: number): number => {
	return x + y;
};

console.log(fn(1, 2));
```

```
type userType = { name: string; age: number };

type addFn = (user: userType) => boolean;

let handleAdd: addFn = (user: userType): boolean => {
	return true;
};

let res:boolean = handleAdd({ name: "tom", age: 10 });
```

---

### 类型断言

主动判定变量是某种类型

```
function fn(arg: number): boolean | number {
	return arg ? true : 0;
}
let res = fn(1) as boolean;

--------------------------------------------

let fn = function (num: number | string): void {
	let length = (num as string).length;
	console.log(length);
};
fn("123"); 			// 3；确定传的会有length属性就能做断言
fn(123); 			// undefined；否则运行时会报错

--------------------------------------------

interface A {
	run: string;
}

interface B {
	build: string;
}

let fn = (type: A | B): void => {
	let res = (<A>type).run;  // 泛型方式；类型断言
	console.log(res);
};

fn({ build: "1" }); 		// undefined
fn({ run: "1" }); 		// 1
```

###### 转值类型

```
let url: string = "www.abc.com";	// 宽泛类型

let user1 = "tom" as const; 		// user1: "tom" 转成值类型
let user1 = <const>"tom";		// 泛型

let user2: "tom" = "tom";		// user2: "tom" 等价于 值类型

const user3 = "tom"; 			// user3: "tom" 等价于
```

###### 数组转元祖

```
const arr = [true, 1, "a"];		// arr: (boolean | number | string)[]

let url: string = "www.abc.com";
let count = 99 as const;		// count: 99;	转值类型，非count: number

let arr2 = [url, true, count] as const;	// arr2: readonly [string, true, 99]
arr2[0] = 'hello';				// err; 只读
```

###### 对象

```
let str: string = "look";
let number = 10 as const;

const obj = {
	name: str, 			// readonly name: string;
	age: number, 			// readonly age: 10; 值类型
} as const;
```

###### 解构

```
// 2. 它只能识别出数组有字符串或函数 - fn: (string | ((x: number, y: number) => number))[]
function fn() {
	let b = (x: number, y: number) => x + y;
	let a = "hello";

	return [a, b];
}

const [n, m] = fn();		// 1. 解构时，ts是不清楚数组里每个下标对应的值类型
m(1, 2);			// 3. err; 字符串无法调用
```

```
const [n, m] = fn() as [string, Function];
console.log(m(1, 2));
```

```
const [n, m] = fn();
let sum = (m as Function)(1, 2);
console.log(sum);
```

```
function fn() {
	let a = "hello";
	let b = (x: number, y: number) => x + y;

	return [a, b] as const;		// 将数组转成元祖
	// or
	return [a, b] as [string, Function];
}

const [n, m] = fn();
let res = m(1, 2);
```

###### 非空

明确不会为 null;

```
const el:HTMLDivElement = document.querySelector('div') as HTMLDivElement;
// 可能是div标签 or null，明确能获取到，断言是div标签；

const el:HTMLDivElement = document.querySelector('div')!	// 把null去掉;
```

###### class

```
class Animal {
	el: HTMLDivElement;
	constructor(el: HTMLDivElement) {
		this.el = el;
	}
	show() {
		return this.el.innerHTML;
	}
}

const el = document.querySelector("div") as HTMLDivElement;
const obj = new Animal(el);

const ell = document.querySelector(".ccc")!;	// ell: Element
const objj = new Animal(ell); 			// err; !只是去除了null,依然不知是哪类标签
```

---

### class

public 属性和方法 默认是 public，子类可覆盖属性和方法；

```
class Animal {
	public name: string;
	age: number;

	constructor(name: string, age: number) {
		this.name = name;
		this.age = age;
	}
	public run() {
		return `${this.name}跑得很快`;
	}
}

const dog = new Animal("dog", 10);
console.log(dog.name, dog.age); 	// dog 10
console.log(dog.run()); 		// dog跑得很快
```

protected 修饰的属性/方法，只能在类访问，子类可访问父类，实例对象无法访问；
子类不不能覆盖父类 protected 属性/方法；

```
class Animal {
	protected name: string;
	public age: number;

	constructor(name: string, age: number) {
		this.name = name;
		this.age = age;
	}
	protected run() {
		return `${this.name}跑得很快`;
	}
}

class Dog extends Animal {
	constructor(a: string, b: number) {
		super(a, b);
	}
	triggerRun() {
		return this.run();
	}
}

const dan = new Dog("Dan", 10);

console.log(dan.age); 			// 10
console.log(dan.run());		// err; 只能在类“Animal”及其子类中访问
console.log(dan.triggerRun()); 	// Dan跑得很快
```

private 修饰私有属性/方法，子类不能继承父类，子类不能覆盖父类，子类相同属性只能改得比父类宽松，实例对象不能访问；

readonly 修饰的属性 只能在 constructor 构造函数中被修改

```
class Dog extends Animal {
	readonly site: string = "http://abc.com";
	static host: string = "http";

	constructor(name: string, age: number, pows: number, site?: string) {
		super(name, age, pows);
		this.site = site || this.site;
	}

	static showSite() {
		return this.host;
	}
}
```

###### 泛型 & class

```
class Collection<T> {
	data: T[] = [];

	public push(...args: T[]) {
		this.data.push(...args);
	}
}

const col = new Collection<string>();
col.push("hello", "word");

const col2 = new Collection<number>();
col2.push(1, 2);

type UserType = {
	name: string;
	age: number;
};

const col3 = new Collection<UserType>();
col3.push({ name: "tom", age: 1 }, { name: "jack", age: 2 });
```

```
class User<T> {
	public constructor(private _user:T) {}

	public get() : T{
		return this._user;
	}
}

interface UserInter {
	name: string;
	age: number;
}

const user1 = new User<UserInter>({ name: "tom", age: 10 });
console.log(user1.get());
```

###### 单列模式

如链接数据库,http 请求等只需产生一个对象；

```
class Axios {
	private static instance: Axios | null = null;

	private constructor() {}

	static make(): Axios {
		if (Axios.instance == null) {
			Axios.instance = new Axios();
			console.log("创建axios实例成功");
		}

		return Axios.instance;
	}
}

// 给构造函数添加了私有属性，new Axios将不能实例化对象，也就等同于不能在外部生成对象；
cosnt http = new Axios();	// err;
const http = Axios.make();
```

###### get & set

```
class Animal {
	private _name: string; // 属性和方法不能同名

	constructor(name: string) {
		this._name = name;
	}
	public get name(): string {
		return this._name;
	}
	public set name(name: string) {
		this._name = name;
	}
	public get firstName(): string {
		return this._name.substring(0, 1);
	}
}

const dog = new Animal("dog");
```

###### 抽象类（少）

-   abstract 加在 class 前面就是抽象类；
-   abstract 类 不能 new 实例化；
-   abstract 类里面的 abstract 属性/方法 必须定义在抽象类中，且 abstract 属性/方法只是定义，不能实现， 子类才必须实现定义父类定义的抽象属性/方法；
-   它和接口的区别不只有抽象的规范待子类实现，还有自身的属性和方法；

```
abstract class Animal {
	abstract name: string;

	abstract run(): void;

	protected getPosition(): number[] {
		return [10, 20];
	}
}

class Dog extends Animal {
	name: string;

	constructor(name: string) {
		super();
		this.name = name;
	}
	run(): void {
		console.log(this.name + "run fast");
	}
}

let dog = new Dog("dog");
dog.run();
```

---

### interface

接口是是一种约定的规范；
接口内的属性和方法必须实现 (除了?):

###### 对象 & interface

###### 数组 & interface

###### 函数 & interface

###### 类 & interface

implement 类型约束 class

```
interface UserInter {
	name: string;
	age: number;
	isLocked: boolean;
}

class User {
	_userInfo: UserInter

	constructor(user: UserInter){
		this._userInfo = user;
	}
	get userInfo(): UserInter {
		return this._userInfo;
	}
}
```

```
interface OptionsInt {
	el: string | HTMLElement;
}

interface DemoInt {
	options: OptionsInt;
	init(): void;
}

interface AnotherInt {
	run(): void;
}

class Animal implements DemoInt, AnotherInt {
	options: OptionsInt;
	constructor(options: OptionsInt) {
		this.options = options;
		this.init();
	}
	init() {}
	run() {}
}
```

###### 泛型 & interface

```
interface ArticleInter<A, B> {
	title: string;
	isLock: A;
	comments: B[];
}

type CommentType = {
	comment: string;
	author?: string;
};

const article: ArticleInter<boolean, CommentType> = {
	title: "how to use ts",
	isLock: true,
	comments: [{ comment: "first learning" }],
};
```

###### 接口继承

-   相同接口名会自动合并

```
interface User {
	name: string;
}
interface User {
	age: number;
}

const obj: User = {
	name: "tom",
	age: 10,
};
```

-   extends 继承接口

```
interface ISay {
	firstName: string;
	lastName: string;
}
interface IRun {
	run();
}

// 声明Imix接口，它继承于ISay 和 IRun接口
interface IMix extends ISay, IRun {}
```

### type

声明类型别名

```
type s = string | number;
type arr = {name: string}[];

// let str:string = 'Tom';
let str:s = 'Tom';


type userType = { name: string; age: number };
type addUserFnType = (user: userType) => boolean;

let handleAddUser: addUserFnType = (userObj: userType) => {
	return true;
};
handleAddUser({ name: "tom", age: 10 });
```

###### type & 交叉类型

```
type userType = {
	name: string;
	age: number;
	city: string;
};

type AnimalType = userType & {
	habit: string;
};

let dog: AnimalType = {
	name: "Dan",
	age: 10,
	city: "Beijing",
	habit: "orange",
};
```

###### type & interface

-   不能定义相同 type 名称;

-   type 只能使用交叉类型去合并类型

```
type Name = {
	name: string
}

type Age = {
	age: number
}

// 交叉类型
type User = Name & Age;
const obj: User = {
	name: 'tom',
	age: 10
}

// 联合类型
type User2 = Name | Age;
const obj2:User2 = {
	name: 'jack'
}
```

###### type & 泛型

```
type CustomType<T> = string | number | T;
let type1: CustomType<boolean> = true;


type PageResult<T> = {
	list: T[];
	page: number;
	pageSize: number;
};

type listItem = {
	id: string;
	name: string;
	age: number;
};

let res: PageResult<listItem> = axios.get({url: ''});
```

###### type & pick

```
type userType = {
	name: string;
	age: number;
	city: string;
};

type AnimalType = Pick<userType, "name" | "city"> & {
	habit: string;
};

let dog: AnimalType = {
	name: "Dan",
	city: "Beijing",
	habit: "orange",
};
```

```
// 声明基本类型别名
type IsOut = boolean;

// 声明联合类型
type Sex = "boy" | "girl";

// type userType = {
// 	name: string;
// 	age: number;
// 	sex?: Sex;
//	isOut: IsOut
// 	show(): string;
// 	[key: string]: any;
// };

interface userType {
	name: string;
	age: number;
	sex?: Sex;
	show(): string;
	[key: string]: any;
}

let obj: userType = {
	name: "tom",
	age: 10,
	sex: "boy",
	show: () => "have 10$",
	city: "北京",
};
```

### 泛型

动态传递类型

```
function dump(arg: string): string {
	return arg;
}
dump('hello');


function dump(arg: number): number {
	return arg;
}
dump(1);
```

```
function dump<T>(arg: T): T {
	return arg;
}

dump(true);			// 不传会自动推断
dump<string>('hello');
dump<number>(1);
```

###### 约束泛型

-   1. 自动类型推断 number、boolean 没有就会报错；

```
function getLength<T>(arg: T): number {
	return arg.length;		// err; string 和 数组才有length属性；
}
```

-   2. 使泛型**继承**字符串或数组 原型上的属性 length 就不会报错，但其他类型依然报错；

```
function getLength<T extends string | any[]>(arg: T): number {
	return arg.length;
}

console.log(getLength("123")); 		// 3
console.log(getLength([1, 2, 3])); 		// 3
console.log(getLength(100)); 			// err
```

-   3. 定义 type 或 interface, 约束类型的范围，传入参数属性上必须有一个 length 属性；

```

type typeLength = { length: number };

function getLength<T extends typeLength>(arg: T): number {
	return arg.length;
}

console.log(getLength([1, 2, 3])); 	// 3; 传入的数组本身就是有length属性；
console.log(getLength(100)); 		// err;
```

-   约束对象的 key

```
let obj = {
	name: "Tom",
	age: 10,
};

type Keys = keyof typeof obj;

function getValue<T extends object, K extends keyof T>(obj: T, key: K) {
	return obj[key];
}

let res = getValue(obj, "name");
console.log(res); // Tom
```

###### 数组 & 泛型

###### 函数 & 泛型

###### class & 泛型

###### interface & 泛型

### 命名空间

-   避免全局污染，隔离变量、函数；
-   通过 export 暴露出来；
-   namespace 所有的变量和方法都需要导出才能访问

```
namespace ios {
	export let a = 1;
	export const sum = (a: number, b: number): number => a + b;

	export namespace Test2 {
		export let aa = 2;
		export const sum = (a: number, b: number): number => a + b;
	}
}

namespace h5 {
	export let b = 3;
}

// 同名合并
namespace ios {
	export let b = 3;
}

console.log(ios.Test2.sum(1, 2));	// 3
console.log(ios.b);			// 3
```

### 装饰器

装饰器有 类装饰器、方法装饰器，属性装饰器，参数装饰器；

-   类装饰器的只接受一个参数 target，接收到的是构造函数;

-   方法装饰器，属性装饰器，参数装饰器的 target:

    -   如将装饰器修饰静态函数，接收的是构造函数；
    -   如将装饰器修饰原型函数，接收原型对象；

-   propertyKey: 修饰的方法名/属性名；

> 构造器有什么优势：不去破坏 class 原有的方法和属性，既然能通过 target 读取到构造函数，就可以在构造函数的原型对象上添加方法和属性；

---

1. 直接使用报错：“作为表达式调用时，无法解析类修饰器的签名”；
2. 需要打开 tsconfig.json 修改装饰器配置，将以下两个属性修改为 true；
3. 执行命令：tsc -w 根据 json 配置项监测文件；

```
"experimentalDecorators": true,
"emitDecoratorMetadata": true,
```

#### 类装饰器

在类的原型链上增加属性和方法；

```
// 1. 定义一个变量，设置它的装饰器为类装饰器
const moveDecorator: ClassDecorator = (target: Function) => {
	// 3. target参数就是装饰器所接收到的类
	console.log(target); 	// [class Tank] 类的构造函数

	target.prototype.getPosition = (): { x: number; y: number } => {
		return { x: 100, y: 100 };
	};
};

// 2. 放在类上一行表示此类使用这个装饰器
@moveDecorator
class Tank {}
// moveDecorator(Tank);	// 不使用@语法糖方式，放在声明的class以下；

const t = new Tank();
console.log((t as any).getPosition());	// 类里面没有方法所以报错，断言一下即可；
```

###### 装饰器叠加

```
const moveDecorator: ClassDecorator = (target: Function) => {
	target.prototype.getPosition = (): { x: number; y: number } => {
		return { x: 100, y: 100 };
	};
};

const musicDecorator: ClassDecorator = (target: Function) => {
	target.prototype.pause = (): void => {
		console.log("播放音乐");
	};
};

@moveDecorator
@musicDecorator
class Tank {}

const t = new Tank();
(t as any).pause();
```

###### 装饰器工厂

意思就是函数颗粒化 或是 闭包；

```
const musicFactorDecorator = (type: number): ClassDecorator => {
	return (target: Function) => {
		if (type === 0) {
			target.prototype.play = (): void => {
				console.log("战争");
			};
		} else {
			target.prototype.play = (): void => {
				console.log("呐喊");
			};
		}
	};
};

@musicFactorDecorator(0)
class Tank {}
new Tank().play(); // 战争

@musicFactorDecorator(1)
class Player {}
new Player().play(); // 呐喊
```

###### 案例 - 处理统一信息

```
const messageDecorator: ClassDecorator = (target: Function) => {
	target.prototype.message = (content: string) => {
		console.log(content);
	};
};

@messageDecorator
class Web {
	public login() {
		// console.log("登陆成功");
		(this as any).message("登陆成功");
	}
}

new Web().login();
```

#### 方法装饰器

放在方法上方就是方法装饰器;

```
const showDecorator: MethodDecorator = (
	target: object, 		// 修饰普通方法是原型对象，修饰静态方法是构造函数
	propertyKey: string, 		// 修饰的方法名
	descriptor: PropertyDescriptor // 对这个方法的描述符（value,可遍历，可修改，可配置）
) => {
	descriptor.value = () => {
		console.log("修改掉修饰的普通方法/静态方法");
	};
};

class User {
	@showDecorator
	public show() {
		console.log("不会被打印");
	}
}

new User().show();	// 修改掉...
```

###### 案例 - 高亮文字

```
const highlightDecorator: MethodDecorator = (
	target: object,
	propertyKey: string,
	descriptor: PropertyDescriptor
) => {
	const method = descriptor.value;
	descriptor.value = () => {
		return `<div style="color:red;">${method()}</div>`;
	};
};

class User {
	@highlightDecorator
	public response() {
		return "开发人员A";
	}
}

let u = new User();
document.body.insertAdjacentHTML("beforeend", u.response());
```

###### 案例 - 统一错误处理

```
const ErrorDecorator: MethodDecorator = (
	target: Object,
	propertyKey: string | symbol,
	descriptor: PropertyDescriptor
) => {
	let method = descriptor.value;
	descriptor.value = () => {
		try {
			method();
		} catch (err) {
			console.log(`%c ${err.message}", "color:green;font-size:33px;`);
		}
	};
};

class User {
	@ErrorDecorator
	findUser() {
		throw new Error("您查找的用户不存在");
	}
}

new User().findUser();
```

###### 案例 - 用户权限验证

装饰器工厂

```
type userType = {
	name: string;
	isLogin: boolean;
	permissions: string[];
};

const user: userType = {
	name: "tom",
	isLogin: true,
	permissions: ["add", "edit", "remove", "list"],
};

const AccessDecorator = (keys: string[]): MethodDecorator => {
	return (
		target: Object,
		propertyKey: string | symbol,
		descriptor: PropertyDescriptor
	) => {
		let method = descriptor.value;
		const validate = () => {
			return keys.every((it) => {
				return user.permissions.includes(it);
			});
		};
		descriptor.value = () => {
			if (user.isLogin === true && validate()) {
				console.log("操作成功");
				return method();
			} else {
				location.href = "login.html";
			}
		};
	};
};

class Article {
	@AccessDecorator(["edit"])
	public handleEdit() {
		console.log("修改操作");
	}

	@AccessDecorator(["add"])
	public handleAdd() {
		console.log("添加操作");
	}
}

new Article().handleAdd();
```

#### (少) 属性装饰器& 参数装饰器

参数装饰器需要安装`npm i reflect-metadata`存/取数据；- 这里没搞

```
// 属性装饰器
const propDecorator: PropertyDecorator = (
	target: object,
	propertyKey: string | symbol
) => {
	console.log(target);
	console.log(propertyKey);
};

// 参数装饰器
const paramsDecorator: ParameterDecorator = (
	target: object,			// 原型对象
	propertyKey: string | symbol | undefined,	// 处于哪个方法
	parameterIndex: number	// 修饰的参数下标位置
) => {
	console.log(`${parameterIndex} 获取参数下标位置`);
};

class Article {

	@propDecorator
	public title: string | undefined;

	public show(id: number, @paramsDecorator content: string) {}
}
```

###### 案例 - 文字处理成小写

```
const lowerWordPropDecorator: PropertyDecorator = (
	target: Object,
	propertyKey: string | symbol
) => {
	let value: string;
	Object.defineProperty(target, propertyKey, {
		get: () => {
			return value.toLocaleLowerCase();
		},
		set: (v) => {
			value = v;
		},
	});
};

class Article {
	@lowerWordDecorator
	public title: string | undefined;
}

const a = new Article();
a.title = 'HELLOW WORLD'
console.log(a.title);	// hello word
```

### 声明文件

使用第三方库时，需要引用它的声明文件，才能获得代码不全、提示等功能；

如引用的库红线没有提示：

-   `npm i --save-dev @types/库名称`，ts 社区为活跃的第三方库编写了声明文件
-   自己添加一个包含"declare module '库名称'"的声明文件`库名称.d.ts`；

```
// 如 express.d.ts
declare module "express" {
	interface Router {
		get(path: string, cb: (req: any, res: any) => void);
	}

	interface App {
		use(path: string, router: any): void;
		listen(port: number, cb?: () => void);
	}

	interface Express {
		(): App;
		Router(): Router;
	}

	const express: Express;

	export default express;
}

// 还可扩充全局变量、函数、class
declare let hello: string;
declare function xxx(params: object): void;
declare class Demo {}
```

```
// 引入文件 main.ts
import express from 'express';

const app = express();
const router = express.Router();
app.use('/api', router);

router.get('/api', (req:any, res:any)=>{
  res.json({code: 200});
})

app.listen(3000, ()=>{
  console.log('visit localhost:3000');
})
```

### 类型兼容

-   协变
    子类型的属性能完全兼容主类型的属性称为协变

```
interface A {
	name: string;
	age: number;
}

interface B {
	name: string;
	age: number;
	sex: string;
}

let a: A = {
	name: "Tom",
	age: 1,
};
let b: B = {
	name: "Jack",
	age: 1,
	sex: "male",
};

a = b;
```

### 泛型工具

-   Partial: 将所有属性变成可选属性；
-   Required: 所有属性变成必选；
-   Pick: 提取部分属性；
-   Exclude: 排除部分属性 (排除联合类型)；
-   Omit: 排除部分属性，并返回新的类型；

```
interface UserInt {
	name: string,
	age: number,
	address: string;
}

type p = Partial<UserInt>

type p2 = Pick<UserInt, "name" | "age">;

type e = Exclude<"a" | "b" | "c", "b" | "c">;
```

-   Record: 约束对象 key 和 value；

```
type Key = "name" | "age" | "sex";
type Value = "Tom" | 10 | "male";

// 第一个泛型约束键名，第二个泛型约束值可以为其中之一；
let obj: Record<Key, Value> = {
	name: "Tom",
	age: "Tom",
	sex: "Tom",
};
```

-   ReturnType: 获取函数返回类型

```
let fn = () => [1, "a", true];

type whichType = ReturnType<typeof fn>;
// type whichType = (string | number | boolean)[]
```
