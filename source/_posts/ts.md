---
title: Ts
---

### 编译环境

- tsc --version: 查看版本，检测是否安装成功；

- 全局安装
  npm install typescript -g
- 局部安装
  限定某个项目的 ts 为特定版本。若全局 ts 不同电脑安装不同版本，从而导致某个特性被废除
  npm install typescript -D

- html 页面中引入 ts 文件，需要通过命令 tsc 文件名（Type Script Compile）编译成 js 文件，最后引入编译后的 js 文件。

- 编译 ts 文件：tsc demo.ts

- 自动编译 ts 文件：tsc demo.ts -w (不会走 tsconfig.json 配置文件，只是编译并监测某个文件)
  <!-- -   在所处目录中 tsc --init 生成 tsconfig.json 文件;
  -   tsc 文件名 -w (监视文件变动); -->
- 生成 tsconfig.json 配置文件： tsc --init

---

### 类型

- 基本类型：number、string、boolean、symbol、null、undefined;

- 根类型：Object(约束只能是简单类型 和 引用类型)、{} (Object 的简写);

- 对象类型：Array、object(约束只能是对象)、function;

- 枚举：enum;

- 其他特殊类型：any、unknown、never、void、元祖、可变元祖;

- 合成类型：联合类型、交叉类型;

---

### 联合类型

```
const arr: boolean | number[] = true; 	// 要么是布尔类型，要么是数值数组类型

let fn = function (val: number | string): number | string {
	return val;
};
```

### 交叉类型

```
type UserType = {
    name: string,
    age: number
}

type AccountType = {
    addr: string
}

type InfoType = UserType & AccountType;

let user: InfoType = {
    name: "Tom",
    age: 1,
    addr: 'No.1 Street'
}

-------------------------------------------------

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

### 值/字面量类型

```
type Flag = 1 | 2;

function fn(type: Flag) {
  if (type) {
    console.log("open");
  } else {
    console.log("close");
  }
}
```

### 类型推断

没有设置类型时，默认会推断类型

```
let any; 				// any: any

let isShown = 1 > 2;			// isShown: boolean
isShown = true;

let greet = ["hello", 99];		// greet:(string|number)[]
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
function run(): never{
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

undefined & null 可以赋值给其他类型

```
let nu: null = null;
let und: undefined = undefined;	  // 非严格模式下，null和undefined可互相赋值

let flag: boolean = true;
flag = null;

let count: number = 10;
count = undefined;
```

### any 类型 & unknown 类型

- any 类型是任何类型的父类(赋值任何类型)，也可以是其他类型的子类(赋值给其他类型)，可获取 any 类型的属性和方法；

- unknown 类型是任何类型的父类，但不能是其他类型的子类；无法获取 unkown 类型的属性和方法；

```
1. any & unknown
2. Object
3. Number & String & Boolean
4. number & string & boolean
5. 1	&	'hello' & true
6. never
```

```
let obj: any = { isShown: true, open: () => 123 };
obj.isShown; 		// 可读取已有属性和方法

obj.run();  		// 可执行，但编译时丢失类型检测，执行时没有的属性和方法才会报错
obj[0].join('-');	// 同上

------------------------------------------------------------------------------

let unk: unknown = { isShown: true, open: () => 123 };
unk.isShown; 				// err；unknown类型无法读属性和方法
let word: string = unk; 		// err；无法把unknown类型赋值给其他类型
let word2: string = unk as string; 	// 断言这个类型是string类型
```

- unknown 一般用作函数参数：用来接受任意类型的变量实参，但在函数内部只用于传递或输出结果，不获取属性的场景

```
function fn(value?: unknown){
    return createFn(value)
}

function createFn(data){... }
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

- 数组 & 泛型

- 数组 & interface

### 元祖类型

元祖类型在定义时，限制了数据的类型/位置/长度;

```
let arr: [string, number, boolean] = ["hello", 2, false];
arr[0] = 99;				// err; 不能更改类型
arr[0] = 'hello world';		// 可改值

let arr: readonly [x: number, y?: boolean] = [1, true];
```

- 元祖标签：元祖元素类型对应的含义不够明确；
- 可变元祖：有些元素像元祖一些是固定位置、不可修改类型、不可修改值，一些元素像数组具有灵活性可修改；

```
let info: [string, number, string, ...any[]] = ["tom", 10, "Beijing", "200kg", "190cm"];
info[4] = "200cm"; 			// 可修改，具有数组灵活性
let tall = info[4];
console.log("tall:", tall); 		// 200cm

解构 ------------------------------------------------------------------------------

let [username, age, city, ...rest]: [string, number, string, ...any[]] =
    ["tom", 10, "Beijing", "200kg", "190cm"];

console.log(rest); 			// ['200kg', '190cm']


元祖标签 --------------------------------------------------------------------------

let [username, age, city, ...rest]: [_username:string, _age:number, _city:string, ...rest:any[]]
	= ["tom", 10, "Beijing", "200kg", "190cm"];

```

### 对象类型

- object 类型

```
let obj: object = 1;		// err; 不能将类型“number”分配给类型“object”

let obj: object = [];		// object约束必须是引用类型；

let obj: object = () => 123;

let obj: object = {
  name: "Tom",
  age: 10,
}

obj.city = 'Beijing'		// err; 类型“object”上不存在属性“city”
```

- 字面量类型

```
let obj: {} = { age: 1 };	// 赋值无效
obj.age = 2;			// err; 类型 {} 上不存在属性 'age'


let obj: {			// 约束obj为对象类型，同时约束只能多少个属性，以及是哪些属性， 并且约束属性值的类型
	name: string;
	age: number;
	hobbit?: string;
};

obj = {				// err；赋值时缺少age属性
	name: 'tom',
	test: true,		// err；obj字面量没有设置test属性
}
```

- 对象取值

```
let obj = {username: 'Tom', age: 1};

// let username = 'username';  // 变量会被修改值；采用常亮
const username = 'username';
let name1 = obj[username];

or -------------------------------------------------

interface User {
  username: string;
  age: number;
}

let obj: User = { username: "Tom", age: 1 };
let username = "username";
let name1 = obj[username as keyof User];
```

- 对象 & interface

### 枚举类型

里面每个元素都有自己的编号，编号从 0 递增，可手动赋值；

```
enum Color {
  red,
  green,
  yellow = 5,
  orange = "orange",
}

let c: Color = Color.orange;

console.log(
  Color[0],     // red
  Color[5]      // yellow
);

console.log(
  c,                // 6
  Color.red,        // 0
  Color.green,      // 1
  Color.orange      // orange
);

let value = Color.green;
console.log(value)  		// 1; 通过key取值；

let key = Color[value];
console.log(key);   		// green; 通过值反向取key；
```

```
enum Status {
  FAIL = 0,
  SUCCESS = 1,
  FINISH = 2,
}

// 形参status为number/string等类型，它属于宽泛类型，仍然不够直观语义化表达具体类型，降低可读性和维护性；
// function bill(status: number): void {
//   if (status === Status.SUCCESS) {
//     console.log("成功");
//   }
// }

function bill(status: Status): void {
  if (status === Status.SUCCESS) {
    console.log("成功");
  } else if (status === Status.FINISH) {
    console.log("完成");
  }
}
```

- 枚举 & interface

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

- 约束变量 fn 必须是函数类型、x，y 形参是 number 类型、返回值是 number 类型；
- 类型声明的 x，y 参数名称只是占位，和实际实现函数的 a，b 形参名称可不一致；
- 类型声明已经有了类型检测和提示，实现函数时，可不必再约束一遍；

```
let sum: (x: number, y: number) => number = (a, b) => {
  return a + b;
};

sum(1, 2);

--------------------------------------------------------------------------

type SumFnType = (x: number, y: number, ...r: any) => any[];

let sum: SumFnType = (a, b, ...rest: any[]) => {
  console.log("The sum is", a + b);
  return rest;
};

console.log(sum(1, 2, 3, 4));		// [3, 4]
```

###### 函数解构

```
type UserType = { name: string; age: number };

type AddFnType = (user: UserType) => string;

let handleAdd: AddFnType = ({ name }: UserType) => {
  return `The username is ${name}`;
};

handleAdd({ name: "tom", age: 10 });
```

###### 约束 this

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

###### 函数重载

一个函数通过参数实现多个方法

```
let arr: number[] = [1, 2, 3];

function findNum(): number[]; // 如没传就是查询全部

function findNum(id: number): number[]; // 传id就是单个查询

function findNum(add: number[]): number[]; // 传数值数组就是添加

// 实现签名；实现签名的形参和返回参类型都必须满足所有的重载签名类型要求；
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

console.log(findNum());		// [1,2,3]
console.log(findNum(1)); 		// [1]
console.log(findNum([4, 5])); 		// [1,2,3,4,5]
```

###### 函数 & interface

###### 函数 & 泛型

---

### 类型断言

主动判定为某种类型，ts 编译时会绕过类型检查，编译器强制转换成另外一个类型；

###### as 类型断言

```
function fn(arg: number): boolean | number {
	return arg ? true : 0;
}
let res = fn(1) as boolean;

------------------------------------------------------------------

let fn = function (num: number | string): void {
	let length = (num as string).length;
};
fn("123"); 			// 确定传的会有length属性就能做断言
fn(123); 			// undefined；否则编辑时丢失检测类型，运行时会报错
```

```
interface A {
	run: string;
}

interface B {
	build: string;
}

let fn = (type: A | B): void => {
	let res = (<A>type).run;    // 类型断言泛型方式；断定type此时调用传递的就是A
	console.log(res)
};

fn({ run: "1" }); 		    // 1
fn({ build: "1" }); 		    // undefined
```

```
function fn() {
	let b = (x: number, y: number) => x + y;
	let a = "hello";

	return [a, b];
}

// 解构时，ts是不清楚导出的数组每个下标对应的类型
// 它只能识别出数组有字符串或函数 fn: (string | ((x: number, y: number) => number) )[]
const [n, m] = fn();
m(1, 2);			// err; m可能是字符串，所以无法调用；

解决方案 ---------------------------------------------

const [n, m] = fn() as [string, Function];
m(1, 2);

or --------------------------------------------------

const [n, m] = fn();
let sum = (m as Function)(1, 2);

or --------------------------------------------------

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

###### ! 非空断言

主动明确不会为 undefined 或 null;

```
function fn(data?: string){
    // data.toString();     	// err; 'data' is possibly 'undefined'

    data?.toString();		// 没有不会执行也就不会报错
    data!.toString();       	// 非空断言，主动去除undefined or null情况，编译时没有了类型检测，运行时才会报错；
    if(data) data.toString();
    (data as string).toString();
}

fn('11');
```

###### ! & as 区别

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

const el = document.querySelector("div") as HTMLDivElement; // el: HTMLDivElement
const obj = new Animal(el);

const ell = document.querySelector(".ccc")!; // ell: Element
const objj = new Animal(ell);
// err; !只是主动去除了null情况, 而constructor(el: HTMLDivElement) 接收的必须是HTMLDivElement标签；
```

### 类型转换

###### as unknown

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

###### as const

简单类型转换后，转成值类型
引用类型转换后，转成只读宽泛类型

```
let user1 = "tom" as const;		// user1: "tom"；从string宽泛类型 转 'tom' 值类型
let user1 = <const>"tom";		// 泛型方式

let user2: user1 = "tom";
let user2: "tom" = "tom";		// 等价于
```

```
let count = 99 as const;			// count: 99;	number宽泛类型 转 99值类型
let url: string = "www.abc.com";

const arr = [true, 1, "a"];			// arr: (boolean | number | string)[]; 这是数组;
let arr2 = [url, true, count] as const;	// arr2: readonly [string, true, 99]; // 这是元祖;
arr2[0] = 'hello';				// err; readonly


const obj = {
	name: 'Tom',
	age: 10 as const,
} as const;

obj.name = 'tom';   				// err; readonly
```

###### 类型递归

```
interface UserType {
  name: string;
  degree: number;
  phone: string;
}

//  in keyof  类型递归，把key和类型都取出来
type CustKeyValType = {
    [K in keyof UserType]: UserType[K];

  // [K in 'name' | 'degree' | 'phone'] : UserType[K];  // 等价于

  // ['name']: UserType['name'];    			 // in 等价于 for...in循环
  // ['degree']: UserType['degree'];
  // ['phone']: UserType['phone'];
};

```

###### 案例 - 为接口增加属性

```
interface UserType {
  name: string;
  phone: string;
}

type AddAttrToUserType<T, K extends string, V> = {
  [P in keyof T | K]: P extends keyof T ? T[P] : V;
};


// type Test = { male: string; name: string; phone: string }
type Test = AddAttrToUserType<UserType, 'male', string>;
```

###### 条件类型

语法形式为 T extends U ? X : Y
其中 T 是待判断的类型，U 是基准类型，T extends U 是条件表达式，X 是条件为真时的类型，Y 是条件为假时的类型；
PS：TypeScript 的基准类型 (boolean、number、string、Array、tuple、enum、any，void、null、undefined、never、object)

```
type ConType<T> = T extends string | number ? T : never;

type TestType1 = ConType<string | number>; 			// TestType1 = string | number

// 泛型方式是将参数循环每个 与 右侧比较
type TestType2 = ConType<string | number | boolean>; 		// TestType2 = string | number

// 字面量方式是将 (string | number | boolean) 作为一个整体 与 右侧进行比较
type TestType3 = string | number | boolean extends string | number	//  TestType3 = never
  ? string
  : never;
```

###### 案例 - 条件类型处理形参

```
function Fn1<T extends object, K extends object>(obj1: T, obj2: K) {}

type GetType<T> = T extends object ? T : never;
function Fn2<T, K>(obj1: GetType<T>, obj2: GetType<K>) {}
```

---

### interface

- 除了?标记外，接口内所有定义的属性和方法必须实现

###### 索引签名 & interface

```
/**
let obj: {
	name: string;
	show?(): string;
	[key: string]: any;		// 索引签名；约束key属性名称为string类型，约束属性值为any类型；
	// [key: string]: number;	// 如值的类型为number就不太合适，它会要求所有属性值都是number类型；
}
*/


interface UserInter {
	name: string;
	show?(): string;
	[key: string]: any;
}

let obj: UserInter = {
	name: "tom",
	age: 10,
	show() {
		return "hello";
	},
	city: "Beijing",
};
```

###### interface 继承

- 相同接口名会自动合并

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

- extends 继承接口

```
interface ISay {
	name: string;
}
interface IRun {
	run();
}

interface IMix extends ISay, IRun {
	sex: string,
	say(): void,
}
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

###### 函数 & interface

```
interface fnInter {
	(price: number): number;
}

let handleTotal: fnInter = (price: number) => price * 2;

or -----------------------------------------------------------

interface AnotherInt {
	init(): void;
	run(): void;
}

class Animal implements DemoInt, AnotherInt {
	constructor() {
		this.init();
	}

	init() {}
	run() {}
}
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

- class & interface

- 泛型 & interface

### type

```
// 声明基本类型别名
type S = string | number;
let str: S = 'Tom';

// 声明联合类型
type Sex = "boy" | "girl";
```

```
type userType = {
	name: string;
	age: number
};

type addUserFnType = (user: userType) => boolean;

let handleAddUser: addUserFnType = (userObj: userType) => {
	return true;
};

handleAddUser({ name: "tom", age: 10 });
```

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

### type & interface 区别

定义对象类型一般采用 interface， 它的优势在于可继承，可命名相同名称合并；

> - interface 只能定义对象类型或接口当名字的类型函数；
> - type 可以定义任何类型，包括类型别名、联合类型、交叉类型、元祖；

> - interface 可以 extends 继承多个 interface，也可以继承 type (罕见)，也可以通过定义相同接口名称来合并接口；
> - type 没有继承功能，只能使用交叉类型去合并到新的类型中；

查阅 `interface 继承`

```
type num = number;

type Name = {
	name: string
}

type Age = {
	age: number
}

类型别名 ---------------------------------------
let count: num = 10;

交叉类型 ---------------------------------------

type User = Name & Age;
const obj: User = {
	name: 'tom',
	age: 10
}

联合类型 ---------------------------------------

type User2 = Name | Age;
const obj2:User2 = {
	name: 'jack'
}

元祖 ---------------------------------------

interface Car { brandNo: string}
interface Plane { No: string }

type VehicleType = [Car, Plane]
```

### 泛型

定义时不明确类型，使用时必须明确具体类型；
泛型形参名称可以是任何字母或单词，常用 T 表示，多个泛型用逗号隔开；

```
function dump(arg: string): string {
	return arg;
}
dump('hello');


function dump(arg: number): number {
	return arg;
}
dump(1);

----------------------------------------------

function dump<T>(arg: T): T {
	return arg;
}

dump(true);			// 不传会自动推断
dump<string>('hello');
dump<number>(1);
```

###### type & 泛型

```
type CustomType<T> = string | number | T;
let type1: CustomType<boolean> = true;

------------------------------------------------------------------------

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

###### interface & 泛型

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

###### 泛型继承

###### 泛型约束

```
function getLength<T>(arg: T): number {
	return arg.length;			// err; string 和 数组才有length属性；
}


-----------------------------------------------------


// 使泛型继承字符串或数组 原型上的length属性
function getLength<T extends string | any[]>(arg: T): number {
	return arg.length;
}
console.log(getLength("123")); 		// 3
console.log(getLength([1, 2, 3])); 		// 3

console.log(getLength(100)); 			// err；但传入的是其他类型依然报错


------------------------------------------------------


// 定义 type 或 interface，约束类型的范围，传入参数必须有length属性；
type typeLength = { length: number };

function getLength<T extends typeLength>(arg: T): number {
	return arg.length;
}

console.log(getLength([1, 2, 3])); 		// 3; 传入的数组本身就是有length属性；
console.log(getLength(100)); 			// err; 这样当调用时就会有类型检测；
```

```
interface Person {
  name: string;
  age: number;
}

type Keys = keyof Person;		 // Keys = 'username' | 'age'
let b: Keys = "age";

------------------------------------------------

let person = { name: "Alice", age: 30 };

type Keys2 = keyof typeof person;	// typeof person 先获得类型，keyof 类型 获取联合类型；
let c: Keys2 = "age";
```

- 约束对象的 key

```
// 第1参 约束泛型T 必须满足object类型（object/Set/Map/函数/Array等），如调用时传string/number等类型检测就会报错；
// 第2参 约束泛型K 必须是第一个泛型T(object对象)的键名之一；
function getValue<T extends object, K extends keyof T>(obj: T, key: K): T[K] {
	return obj[key];
}

let res = getValue(obj, "name");
console.log(res); 				// Tom
```

- 解决 keyof 不能直观看到联合类型；

```
type Obj = {
	username: "Tom";
	age: 1;
};

// extends 会将左侧循环与右侧进行比较，所以能够ObjKeys2能直接看到key联合类型
type ViewKeys<T> = T extends any ? T : never;
type ObjKeys2 = ViewKeys<keyof Obj>;    	// ObjKeys2 = "username" | "age"
```

###### 泛型嵌套

###### 数组 & 泛型

```
let arr: Array<number | string | object> = [1, "2", {}];
let arr: (number | string | object)[] = [1, 'hello', {id: 1}];	// 等价于

let arr: Array<Array<{ id: number }> | boolean> = [[{ id: 1 }], true];
let arr: ({id: number}[] | boolean)[] = [[{id: 1}, {id: 2}], true];		// 等价于
```

###### 函数 & 泛型

```
function add<T, K = boolean>(a:T, b: K): Array<T | K> {
	return [a, b]
}

add(1, true)
```

###### Promise & 泛型

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

###### class & 泛型

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

### class

- ES6 的 class

```
class Animal {
	// 静态属性
	static paws = 4;

	constructor(name) {
		// 实例对象共有属性
		this.name = name;
		// 创建实例对象就立即调用
		this.init();
	}

	init(){
		console.log('initial finished');
	}

	// 实例对象共有方法
	run(method) {
		console.log(this.name, `is runing ${method}`);
	}

	// 静态方法
	static eat(){
		console.log('chips');
	}
}

console.log(Animal.paws);  // 4

// 生成实例对象
var dog = new Animal("dog");
var cat = new Animal("cat");

console.log(dog.name); // dog
console.log(cat.name); // cat

dog.run("fast");
cat.run("slow");
```

- Ts 中的 class

```
class Animal {
  // ts中的类的属性 在类体中必须也有，不像ES6中的类只需有在构造函数中；
  name: string;
  age: number;

  /**
   *  err; 属性addr没有初始化表达式，且未在构造函数中明确赋值;
   *  要么在这里给可选值   	addr?: string;
   *  要么忽略必须的初始值 	addr!: string;
   *  要么在这里给初始值   	addr: string = 'No.10 Street';
   *  要么在构造函数中赋初始值 	this.addr = _addr;
   */
  addr: string;

  // 类的静态属性
  static paws: number = 0;

  constructor(_name: string, _age: number, _addr: string) {
    this.name = _name;
    this.age = _age;
    this.addr = _addr;
    Animal.paws++;
  }

  run() {
    console.log("run faster");
    Animal.paws++;
  }
}

console.log(Animal.paws); // 0

let dog = new Animal("dog", 1, "Beijing");
let cat = new Animal("cat", 2, "Beijing");

console.log(dog.name); // dog
console.log(cat.name); // cat
cat.run();

console.log(Animal.paws); // 3
```

- public 属性和方法 默认是 public，子类可覆盖属性和方法；

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

- protected 修饰的属性方法，只能在类访问；实例对象无法访问；子类可访问父类，但不能覆盖父类 protected 属性方法；

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

- private 修饰私有属性方法，实例对象不能访问；子类不能继承父类 且 不能覆盖父类私有属性方法，子类相同属性只能改得比父类宽松；

- readonly 修饰的属性 只能在 constructor 构造函数中被修改；

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

###### 单列模式

如链接数据库，http 请求等，类只需产生一个对象；

```
// static 方式

export default class DateTool {
  // 无需创建多个对象，只需访问类这个函数的对象上的静态方法；
  static formateData() {}
}

DateTool.formateData();			// 调用
```

```
// 一个类只允许获取一个实例对象；

class Axios {
	private static instance: Axios | null = null;

	// 将类的构造函数设为私有，只允许在类的内部调用，外部将不容许实例化对象；
	private constructor() {}

	static make(): Axios {
		if (Axios.instance == null) {
			Axios.instance = new Axios();
			console.log("创建axios实例成功");
		}

		return Axios.instance;
	}

	formateData() {}
}

// cosnt http = new Axios();			// err; 不能在外部实例化对象；
const http = Axios.make();
export default http;

http.formateData();				// 调用
```

###### get & set

```
class Animal {
  private _age!: number;
  public name: string;

  constructor(name: string) {
    this.name = name;
  }

  get age(): number {
    return this._age;
  }

  // 可以对某个属性说明 和 对属性值的控制；
  set age(val: number) {
    if (val > 0 && val < 120) {
      this._age = val;
    } else {
      throw new Error("年龄不在合适的范围");
    }
  }
}

try {
  const dog = new Animal("dog");
  console.log(dog.name);
  dog.age = -1;
} catch (err) {
  console.log(err);
}
```

###### class & interface

```
interface DemoInt {
	options: OptionsInt;
}

interface AnotherInt {
	init(): void;
	run(): void;
}

interface OptionsInt {
	el: string | HTMLElement;
	url: string,
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

- class & 泛型

###### 抽象类（少）

- abstract 加在 class 前面就是抽象类；
- abstract 类 不能 new 实例化；
- abstract 类里面的 abstract 属性/方法 必须定义在抽象类中，且 abstract 属性/方法只是定义，不能实现， 子类才必须实现定义父类定义的抽象属性/方法；
- 它和接口的区别不只有抽象的规范待子类实现，还有自身的属性和方法；

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

### 命名空间

- 避免全局污染，隔离变量、函数；
- 通过 export 暴露出来；
- namespace 所有的变量和方法都需要导出才能访问

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

- 类装饰器的只接受一个参数 target，接收到的是构造函数;

- 方法装饰器，属性装饰器，参数装饰器的 target:

  - 如将装饰器修饰静态函数，接收的是构造函数；
  - 如将装饰器修饰原型函数，接收原型对象；

- propertyKey: 修饰的方法名/属性名；

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

### infer

- infer 表示在 extends 条件语句中以占位符出现，等使用时才推断出来的数据类型，infer + 任意字母占位符；

```
interface User {
    name: string
    age:number
}

type Fn = (params: User) => number;

// 获取函数的参数类型
// Fn 函数返回值与这里继承的函数返回类型一致，就返回infer获取到的参数类型，否则返回Fn函数；
type ParamsType1 = Fn extends (params: infer P) => number ? P : Fn;
type ParamsType2 = Fn extends (params: infer P) => string ? P : Fn;

let user1: ParamsType1 = {
    name: 'Tom',
    age: 1,
}

let userFn: ParamsType2 = (user: User) => {
    return user.age;
}

----------------------------------------------------------------------

// 获取函数的返回值类型
type ReturnNumberType = Fn extends (params: User) => infer R ? R : Fn;
let num: ReturnNumberType = 10;


----------------------------------------------------------------------

// 通用型
type ReturnNumberType<T> = T extends (params: any) => infer R ? R : never;
let num: ReturnNumberType<Fn> = 10;

----------------------------------------------------------------------

// infer可作泛型占位符，获取泛型类型
type TypeOfArr<T> = T extends Array<infer P> ? P : never;
let a: TypeOfArr<Array<number>> = 1;
let b: TypeOfArr<Array<{ name: string }>> = { name: "Tom" };
```

### 工具类型

- Partial: 将所有属性变成可选属性；
- Required: 所有属性变成必选；
- Pick: 从 interface\type\class 中提取部分属性，并组成新的对象类型；
- Exclude: 排除部分属性 (排除联合类型)；
- Omit: 排除部分属性，并返回新的类型；
- Extract: 条件类型，用于从两个类型中提取它们的交集部分；
- Record: 用于创建一个对象类型，约束对象的 key 名称 和 值类型（适合替代 type 或 interface 情况下）；

---

- readonly

```
type UserType = {
  readonly name: string;
};

type NoReadonly<T> = {
  readonly [K in keyof T]: T[K];	// 添加readonly
  -readonly [K in keyof T]: T[K];	// 去除readonly
};

let user: NoReadonly<UserType> = {name: 'Tom'}
user.name = 'Jack'
```

- Partial

```
type PageParams = {
  page: number;
  pageSize: number;
};

type p = Partial<PageParams>

源码实现 -------------------------------------------

type OriginPartial<T> = {
  [K in keyof T]?: T[K];
};

let p: OriginPartial<PageParams> = {};
```

```
interface UserInt {
	name: string,
	age: number,
	address: string;
}

type e = Exclude<"a" | "b" | "c", "b" | "c">;		//  e = "a"

type e = Extract<string | number | boolean, string | number>;	// e = string | number
```

- Pick

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

- Required

```
type PageParams = {
  page?: number
  pageSize?: number
}

// 利用Required将原本的可选变成必选，此时如果缺少字段就会报错；
const params: Required<PageParams> = {
  page: 1,
  pageSize: 10,
}


源码实现 ----------------------------------------------------

type OriginRequired<T> = {
  [K in keyof T]-?: T[K];
};

type PageParams = {
  page?: number;
  pageSize?: number;
};

let p: OriginRequired<PageParams> = { page: 1, pageSize: 10};
```

- Record: 约束对象 key 和 value；

```
type Key = "name" | "age" | "sex";
type Value = "Tom" | 10 | "male";

// 第一个泛型约束键名，第二个泛型约束值可以为其中之一；
let obj: Record<Key, Value> = {
	name: "Tom",
	age: "Tom",
	sex: "Tom",
};

-----------------------------------------------------------

function fn(info: Record<'name' | 'age', string | number>){}
fn({name: 'Tom', age: 1})
```

- ReturnType: 获取函数返回类型

```
let fn = () => [1, "a", true];

type whichType = ReturnType<typeof fn>;	// whichType = (string | number | boolean)[]
```

---

### 声明文件

> 在 TS 文件编码，在编译成 JS 文件时会自动生成声明文件 d.ts; 声明文件只对类型定义，不进行任何的赋值和实现。

使用第三方库时，需要引用它的声明文件，才能获得代码补全、结构提示等功能；

如引用的第三方库显示红线且没有提示，需引入其声明文件 或 自己添加声明文件；

> - ts 社区为活跃的第三方库编写了声明文件：`npm i --save-dev @types/库名称`
> - 自己添加声明文件：`库名称.d.ts`
>   PS: ts 会自动在 tsconfig.json > include 指定的文件下寻找到对应的声明文件；

```
// 实现声明文件

declare let/const	// 声明全局变量
declare function	// 声明全局方法 (declare声明的函数可重名)
declare class		// 声明全局类
declare enum		// 声明全局枚举
declare namespace	// 声明命名空间
interface/type		// 声明全局类型和接口 (无需declare关键字)
```

```
// 模块声明定义声明文件 declare module '模块名称'，如 express.d.ts；

declare module "express" {

	interface Express {
		(): App;
		Router(): Router;
	}

	interface App {
		use(path: string, router: any): void;
		listen(port: number, cb?: () => void);
	}

	interface Router {
		get(path: string, cb: (req: any, res: any) => void);
	}


	const express: Express;

	export default express;		// 导出模块
}
```

```
// main.ts
import express from 'express';		// 导入第三方库（会自动寻找express.d.ts声明文件）

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

- 协变
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

### tsconfig.json

```
{
	// 编译选项
	"compilerOption": {
		"lib": ["esnext",  "DOM"],		// 哪个库, 如 New Set() 是es6出来的，console方法是 'DOM'提供的;
		"target": "ESNext", 	// ts按照哪个版本编译成js，如 "es2015" 按照es5编译，   "es2016" 按照es6编译;
		"module": "CommonJS", // 编译成js使用哪种规范
		"rootDir": "./demo", // 指定编译输入目录
		"outDir": "./dist", // 指定编译后输出目录
		"resolveJsonModule": true, // ts中，允许引入json文件
		"allowJs": true, // ts中，允许引入js文件
		"checkJs": true, // ts中，如果引入了js，会把js文件当成ts，是否使其被语法检测
		"declaration": true, // ts生成声明文件，输入目录中会多一个对应的 文件.d.ts 声明文件

		"strict": true, // 是否开启严格模式
		"noImplicitAny": true, // 是否允许 不能有any类型
		"strictNullChecks": true, // 是否允许 不能赋值 null 和 undefined 类型
		<!-- "strictPropertyInitialization": true, // 属性声明时必须要初始值 -->
		"noUnusedLocals": true, // 是否允许 不能有未使用的变量
		"noUnusedParameters": true, // 是否允许 不能有未使用的形参

		"skipLibCheck": true, // 是否跳过声明文件作了实现的检测（声明文件只声明，不实现）
		"jsx": "preserve", // 是否允许jsx
		typeRoots: ["node_modules/@types"], // 引入第三方插件时，指定它们的声明文件地址在哪儿

		// 注意：这里设置的@别名只是ts编译没问题，而运行别名 需要在vite.config.js中配置别名
		"baseUrl": "./", // 基础路径，.代表工程根目录；
		"paths": {	// 映射路径，path设置的路径会根据baseUrl查找子目录
			"@/*": ["./src/*"], // import a from '@/components/index.vue' 表示 './src/components/index.vue';
			"~component/*": ["./src/components"/*], //  import a from '~components/index.vue' 表示 './src/components/index.vue'
		},
	},
	"include": [], // 指定哪些文件和目录被ts编译
}
```
