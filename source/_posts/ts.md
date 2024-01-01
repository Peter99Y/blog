---
title: Ts
---

### 编译环境

-   全局安装
    npm install typescript -g
    mac 系统 brew install typescript

-   局部安装
    限定某个项目的 ts 为特定版本，不像全局 ts 不同电脑安装不同版本，从而导致某个特性被废除
    npm install typescript -D

-   tsc -v 查看版本

-   html 页面中引入 ts 文件，需要通过命令 tsc 文件名（Type Script Compile）编译成 js 文件，最后引入编译后的 js 文件。

-   编译 ts 文件：tsc demo.ts

-   自动编译 ts 文件：tsc demo.ts -w
    <!-- -   在所处目录中 tsc --init 生成 tsconfig.json 文件;
    -   tsc 文件名 -w (监视文件变动); -->
-   生成 ts 配置文件 tsc --init

---

### 类型推断

没有设置类型时，默认会推断类型

```
let isShown = 1 > 2;				// isShown: boolean
isShown = true;

let info;                  			// any类型
info = {name: 'tom'};
info = true;

let count = 12;           			// count:number
count = 'a';					// err

const greet = ["hello", 99];			// greet:(string|number)[]
greet.push(true);				// err

const user = { name: "tom", age: 10 };	  	// user:{age: number}
user.age = "10";		  	  	// err

function sum(a: number, b: number) {	  	// sum(a:any,b:any):string
  return '结果是：' + a + b;
}

function fn(arg: boolean): string | number {
	return arg ? "hello" : 100;
}
let res = fn(true); 				// res: string | number
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

值只能是 null 或 undefined; 常用于函数返回值;

```
let str:void = null;
str = undefined;

function show():void{}

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
let unde: undefined = undefined;

let flag: boolean = true;
flag = null;

let count: number = 10;
count = undefined;
```

### 数组类型

与元祖相比，不限制长度和位置，只限制里面的类型

```
const arr string[];

const arr: number[] = [1, 2, 3];	// 数值数组

const arr: string | number[];		// 要么是字符串，要么是数值数组

const arr : (string | boolean)[] = ['hello', 'word', true];	// 数组可包含字符串或布尔值

let arr: { id: number }[] = [{ id: 1 }, { id: 2 }, { id: 3 }];	// 对象数组
arr.push(1);							// err

const arr: Array<number | string | object> = [1, "2", {}];	// 使用泛型设置数组
```

### 元祖类型

元祖类型在定义时，限制数组数据的类型/位置/长度;

```
let arr: [string, number, boolean] = ["1", 2, false];
arr[0] = 99				// err; 可改值，不能更改类型
```

### 对象类型

```
let obj:object;			// 限定obj为对象类型

let obj: {			// 限定obj为对象类型，同时限定了3个属性，以及限定每个属性值类型
	name: string;
	age: number;
	hobbit?: string; 	// 有or没有属性都可
};

obj = {				// err 缺少age属性
	name: 'tom',
	test: true,		// err obj没有设置test属性
}
```

### any 类型

不清楚变量是什么数据类型 or 很多类型都可以使用不限定类型，不需要进行类型校验（但也就失去了 ts 的意义了）

```
let any :string | number | object | boolean = 'hello';
let any : any = 'hello';

let arr: any[] = [{}, [], 1, false, "a"];
let arr: Array<any> = [];


// 缺点：编译时没有了类型检测
console.log(arr[0].split(''))	// err
```

### unkonwn 类型

不清楚什么类型，但是有类型的；

```
let a: any = "hello";
let word: string = a;

let b: unknown = "hello";
let word2: string = b; 		// err；把一个不知道的类型赋值给字符串就会报错

let word3: string = b as string; 	// 明确说明这个类型是string类型
```

#### 类型转换

值没有问题的，转换类型

```
let str: string = "99";
let a: number = str as number; 		// err; 把string不能直接转成number类型
let b: number = str as unknown as number;   	// 但可以先转成未知类型，再把未知类型转成number类型
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

### 函数

```
let fn: Function = (a: number, b: number) => "结果是" + a + b;
```

```
let sum = (a: number, b: number, c: number = 0.6, d?: number): number => {
	return (a + b) * c;
};

let res: string = sum(1, 2) as unknown as string;
```

函数结构定义

```
let sum: (a: number, b: number) => number; 	// 定义函数

sum = (x, y) => {       			// 需要实现的函数；形参可不一致
	return x + y;
};

sum(1, 2);
```

```
function sum(...args: number[]): number {
	return args.reduce((p, i) => p + i, 0);
}

console.log(sum(1, 2, 3));
```

### type

```
let addUser = (user: { name: string; age: number }): void => {
	console.log("添加用户");
};
let updateUser = (user: { name: string; age: number }): void => {
	console.log("更新用户");
};
```

对对象声明/定义

```
type userType = {
	name: string;
	age: number;
	sex?: string | number
};

let addUser = (user: userType): void => console.log("添加用户");

let updateUser = (user: userType): void => console.log("更新用户");

class Person implements userType {
	name: string
	age: number
	constructor(name:string, age:number){
		this.name = name;
		this.age = age;
	}
}
```

对函数声明/定义

```
type userType = { name: string; age: number }; // 声明一个对象结构

type addUserFnType = (user: userType) => boolean; // 声明一个函数结构，userType对象约束参数

let handleAddUser: addUserFnType = (u: userType) => {    // addUserFnType约束变量，需要的是一个函数
	console.log("添加用户成功", u);
	return true;
};

handleAddUser({ name: "tom", age: 10 });
```

### type & interface

-   不能定义相同 type 名称;

-   type 可以合并 type;

```
type Name = {
	name: string
}

type Age = {
	age: number
}

// type合并
type User = Name & Age;
const obj: User = {
	name: 'tom',
	age: 10
}

type User2 = Name | Age;
const obj2:User2 = {
	name: 'jack'
}

class Person implements User {
	name: string = 'tom';
	age: number = 10;
}
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

### 断言

主动判定变量是某种类型

```
function fn(arg: number): boolean | number {
	return arg ? true : 0;
}

let res = fn(1) as boolean;
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

###### abstruct 抽象类

    1.抽象属性/方法必须存在于抽象类中，相当于父类定义了规范，子类必须定义抽象属性/方法;
    不能 new 抽象类实例化对象，它只是提供规范;
    2.它和接口的区别不只有抽象的规范待子类实现，还有自身的属性和方法;

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

###### 对象接口

```
interface UserInter {
	name: string;
	age?: number,
	show?(): string;
	[key: string]: any; // 这里的any指的是对象的属性值可以是任何宽泛类型
}

// let obj: { name: string; age: number; show?(): string; [key: string]: any } = {
// 	name: "tom",
// 	age: 10,
// 	show() {
// 		return "hello";
// 	},
// 	city: "Beijing",
// };

let obj: UserInter = {
	name: "tom",
	age: 10,
	show() {
		return "hello";
	},
	city: "Beijing"
};
```

###### 数组 & 枚举接口

```
enum SexType {
	Girl,
	Boy
}

interface UserInter {
	name: string;
	age: number;
	sex: SexType
}

let user1: UserInter = {
	name: "tom",
	age: 10,
	sex: SexType.Boy
};

let user2: UserInter = {
	name: "jack",
	age: 10,
	sex: SexType.Girl
};

const arr: UserInter[] = [user1, user2];
console.log(arr);		// [ { name: 'tom', age: 10, sex: 1 } ...]
```

###### 函数接口

```
// 定义接口
interface Good {
	(price: number): number;
}

// 对函数的约束
let handle: Good = (price: number) => price * 2;
```

```
// 定义接口
interface UserInter {
	name: string;
	age: number;
	isLocked: boolean;
}

// 对对象约束
const user: UserInter = {
	name: "tom",
	age: 10,
	isLocked: false,
};

// 对形参和返回值约束
function handleLock(user:UserInter, lock: boolean): UserInter{
	user.isLocked = lock;
	return user;
}

handleLock(user, true);
```

###### 类的接口

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

extends or implements 继承接口

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

class 实现多个接口约束

```
// class Person implements IMix{}

class Person implements ISay, IRun {
	firstName: string;
	lastName: string;

	constructor(firtname: string, lastname: string) {
		this.firstName = firtname;
		this.lastName = lastname;
	}
	run() {
		console.log("我可以跑10公里");
	}
}

new Person("tom", "k");
```

---

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

###### 对泛型约束

T 是根据动态传递的类型，如 string 和 any[] 都有 length 属性，但 number 是没有的;

```
function getLength<T>(arg: T): number {
	return arg.length;
}
```

约束泛型只能是字符串或者数组两种类型，不能是其他类型

```
function getLength<T extends string | any[]>(arg: T): number {
	return arg.length;
}

// or 我们自己添加个类型（此类型必须包含length属性）
// function getLength<T extends { length: number }>(arg: T): number {
// 	return arg.length;
// }

getLength<string>("hello");
getLength<any[]>([1, 2, 3]);
getLength<number>(10)	// err
```

约束数组成员

```
function getArr<T>(arg: T[]):number{
	return arg.length;
}

console.log( getArr<string | number>(['hello', 2, 3]) )
```

###### class

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

###### interface

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

---

### 装饰器

请先打开 tsconfig.json 修改装饰器配置；
tsc -w 监视根据 json 文件监视 ts；

```
"experimentalDecorators": true,
"emitDecoratorMetadata": true,
```

-   类装饰器的 target 接收构造函数;
-   方法装饰器，属性装饰器，参数装饰器的 target:
    -   修饰静态函数，接收的是构造函数；
    -   修饰原型函数，接收原型对象；
-   propertyKey: 修饰的方法名/属性名；

###### 类装饰器

```
const moveDecorator: ClassDecorator = (target: Function) => {
	target.prototype.msg = 'hello';

	target.prototype.getPosition = (): { x: number; y: number } => {
		return { x: 100, y: 100 };
	};
};

// 类放置在第二排作为装饰器参数将类的构造函数传递;
@moveDecorator
class Tank {
	public getPosition() {}
}
const t = new Tank();
console.log(t.getPosition());
```

不使用@语法糖方式

```
class Player {
	public getPosition() {}
}
const p = new Player();
moveDecorator(Player);

console.log(p.getPosition());
```

###### 装饰器工厂

```
const MusicDecoratorFactory = (type: string): ClassDecorator => {
	switch (type) {
		case "Tank":
			return (target: Function) => {
				target.prototype.playMusic = (): void => {
					console.log("tank music");
				};
			};
		default:
			return (target: Function) => {
				target.prototype.playMusic = (): void => {
					console.log("default music");
				};
			};
	}
};

@MusicDecoratorFactory("Tank")
class Tank {
	public playMusic() {}
}
const t = new Tank();
t.playMusic();


@MusicDecoratorFactory("Player")
class Player {
	public playMusic() {}
}
const p = new Tank();
p.playMusic();
```

###### 方法装饰器

放在方法上面修饰方法就是方法装饰器;

```
type userType = {
	name: string;
	isLogin: boolean;
	permissions: string[];
};

const user:userType = {
	name: "tom",
	isLogin: true,
	permissions: ["edit"],
};

const HighLightDecorator: MethodDecorator = (
	target: Object,
	propertyKey: string | symbol,
	descriptor: PropertyDescriptor
) => {
	// 获得修饰的方法;
	const method = descriptor.value;
	descriptor.value = () => {
		return `<h3 style="color:red;">${method()}</h3>`;
	};
};

// 装饰器工厂
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
				return method();
			} else {
				location.href = "login.html";
			}
		};
	};
};

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
			console.log("%c hello", "color:green;font-size:33px;");
		}
	};
};

class Article {
	@HighLightDecorator
	public response() {
		return "hello world";
	}

	@AccessDecorator(['add', 'edit', 'remove', 'delete'])
	public handleEdit(){
		console.log('执行修改操作');
	}

	@ErrorDecorator
	public handleErr() {
		throw new Error("false");
	}
}
document.body.insertAdjacentHTML("beforeend", new Article().response());
new Article().handleErr();
```

###### 属性装饰器

```
const lowerWordDecorator: PropertyDecorator = (
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
