---
title: Ts
---

-   html 页面中引入 ts 文件，需要通过命令 tsc 文件名（Type Script Compile）编译成 js 文件，最后引入编译后的 js 文件。

-   vscode 自动编译 ts
    -   在所处目录中 tsc --init 生成 tsconfig.json 文件;
    -   tsc 文件名 -w (监视文件变动);
    -   tsc -w (根据 json 文件监视项目文件);

---

###### 类型推断

会默认先赋值的类型

```
let info;                  	// any类型
info = {name: 'tom'};

let count = 12;           	// 默认推断number类型
count = 'a';			// err
```

###### undefined & null 类型

undefined & null 都可以作为其他类型 (ts 配置文件可禁用被赋值给其他类型);

```
let flag: boolean = true;
flag = null;

let count: number = 10;
count = undefined;

let str: string = "小明";

let nu: null = null;
let un: undefined = undefined;
```

###### 数组类型

```
const arr string[];

const arr: number[] = [1, 2, 3];

const arr : (string | boolean)[] = ['hello', true];

区分：
const arr: string | number[];	// 字符串 or 数字数组

const arr: Array<number | string | boolean> = [1, "2", false];
```

###### 元祖类型

元祖类型在定义时，限制数组数据的类型/位置/长度;

```
let arr: [string, number, boolean] = ["1", 2, false];
```

###### object 类型

```
let obj:object;

let obj: {
	name: string;
	age: number;
	hobbit?: string; // 有or没有属性都可
};

obj.name = 'tom';
obj.age = 10;
```

###### any 类型

不清楚变量是什么数据类型 or 很多类型都可以使用，不需要进行类型检测

```
let any :string | number | object | boolean = 'hello';

let any : any = 'hello';

let arr: any[] = [{}, [], 1, false, "a"];


// 缺点：编译时没有了类型检测
console.log(arr[0].split(''))	// err
```

###### unkonwn 类型

```
let key: unknown = "hello";
// let b: string = key;		// err
let b: string = key as string;		// 使用类型断言（断定key是string类型）
```

###### 枚举类型

里面每个数据都可以叫元素，每个元素都有自己的编号，编号从 0 递增，可手动赋值；

```
enum Color {
	red,
	green,
	blue,
}

console.log( Color[0] ); 	// red

console.log(
	Color.red,		// 0; 访问的是编号
	Color.green,		// 1
	Color.blue		// 2
);

let c: Color = Color.red;
console.log(c)        		// 0
```

###### void 类型

值是 null，undefined; 常用于函数返回值;

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

###### never 类型

```
function run():never{
	throw new Error('错误');
}

run();
```

###### 函数

```
let fn: Function = () => 'hello';
```

```
let sum = (a: number, b: number): string => {
	return `a+b=${a + b}`;
};

let res: string = sum(1, 2);
```

###### 函数 剩余参数

```
function sum(...args: number[]): number {
	return args.reduce((p, i) => p + i, 0);
}

console.log(sum(1, 2, 3));
```

### type

```
type userType = {
	name: string;
	age: number;
	sex?: string | number
};

let add = (user: userType): void => console.log("添加用户");

class Person implements userType {
	name: string
	age: number
	constructor(name:string, age:number){
		this.name = name;
		this.age = age;
	}
}
```

###### type & interface

-   type 可以合并 type;
    type 实现继承不能重复定义相同名；

```
type Name = {
	name: string
}

type Age = {
	age: number
}

type User = Name & Age;
type User2 = Name | Age;

const obj:User = {
	name: 'tom',
	age: 10
}

const obj2:User2 = {
	name: 'jack'
}
```

```
// 声明基本类型别名
type isOut = boolean;
// 声明联合类型
type Sex = "boy" | "girl";

// type userType = {
// 	name: string;
// 	age: number;
// 	sex?: Sex;
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

###### 函数原型

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

### 断言

as 主动判定变量是某种类型

```
function fn(arg: number): boolean | number {
	return arg ? true : 0;
}

let res = fn(1) as boolean;
```

```
let gender : 'male' | 'female'; // 限定值类型
gender = 'abc';			// err
gender = 'male';
```

###### as const

```
// 宽泛类型(限制值类型)
let url: string = "www.abc.com";

// 值类型(限制值)
let name1: "tom" = "tom";
const name2 = "tom"; 		// 等价于
let name3 = "tom" as const; 	// 等价于
```

###### 数组中断言

```
const arr = [true, 1, "a"];
// (boolean | number | string)[] (数组)(宽泛类型);

let url: string = "www.abc.com";
const arr2 = [url, true, 1] as const;
// [string, true, 1]  (只读; 把数组转元祖)(值类型);
```

###### 对象中断言

```
let str: string = "look";
let number = 10 as const;

const obj = {
	name: str,		// 只读 string (宽泛类型)
	age: number,		// 只读 10 (值类型)
} as const;
```

###### 解构断言

```
function fn() {
	let a = "hello";
	let b = (x: number, y: number) => x + y;

	return [a, b];
}

const [n, m] = fn() as [string, Function]
let res = m(1, 2);
```

```
function fn() {
	let a = "hello";
	let b = (x: number, y: number) => x + y;

	return [a, b] as [typeof a, typeof b];
	return [a, b] as const;		// 将数组转成元祖
}

const [n, m] = fn();
let res = m(1, 2);
```

###### 非空断言

明确不会为 null;

```
const el:HTMLDivElement = document.querySelector('div') as HTMLDivElement;
// 如果没获取到div标签，就是null，断言就是div标签；

const el:HTMLDivElement = document.querySelector('div')!// 把null去掉;
```

-   class 断言

```
class Animal{
	el: HTMLDivElement
	constructor(el:HTMLDivElement){
		this.el = el;
	}
	show(){
		return this.el.innerHTML;
	}
}

const el = document.querySelector('div') as HTMLDivElement;
const obj = new Animal(el);
// const el = document.querySelector('.ccc')!;
// err; 非null，推断是Element，而不是HTMLDivElement
```

### class

public 默认
protected 修饰的属性/方法，只能在类的原型方法上调用，继承的父类上的也可访问 ，实例对象不能访问；
private 修饰私有属性/方法，不能调用继承父类的，子类不能覆盖，实例对象不能访问；
readonly 修饰的属性 只能在 constructor 构造函数中被修改

```
class Animal {
	public name: string;
	public age: number;

	protected pows: number;
	private popular: string = "欢迎";

	constructor(name: string, age: number, pows: number) {
		this.name = name;
		this.age = age;
		this.pows = pows;
	}
	protected run() {
		console.log(`${this.name} 跑得快，它非常${this.popular}`);
	}
}

class Dog extends Animal {
	readonly site: string = "http://abc.com";
	static host: string = "http";

	constructor(name: string, age: number, pows: number, site?: string) {
		super(name, age, pows);
		this.site = site || this.site;
	}

	public show() {
		// console.log(this.popular);	// err // 私有属性不能被继承调用
		this.run();
	}

	static showSite() {
		return this.host;
	}
}

let dog = new Dog("dog", 10, 4);
dog.show();
console.log(Dog.showSite());
```

###### 单列模式

如链接数据库,http 请求等只需产生一个对象；

```
class Axios {
	private static instance: Axios | null = null;

	// 给构造函数添加了私有属性，new Axios将不能实例化对象，也就等同于不能在外部生成对象；
	private constructor() {}

	static make(): Axios {
		if (Axios.instance == null) {
			Axios.instance = new Axios();
			console.log("创建axios实例成功");
		}

		return Axios.instance;
	}
}

const http = Axios.make();
```

###### get & set

```
class Animal{
	private _name: string
	constructor(name:string){
		this._name = name;
	}
	public get name():string{
		return this._name;
	}
	public set name(name:string){
		this._name = name;
	}
	public get firstName():string{
		return this._name.substring(0,1)
	}
}

const dog = new Animal('dog');
console.log(dog.name)
console.log(dog.firstName)
```

###### abstruct 抽象类

    抽象属性/方法必须存在于抽象类中，相当于父类定义了规范，子类必须定义抽象属性/方法;
    不能 new 抽象类实例化对象（只是提供规范）

```
abstract class Animal {
	abstract name: string;
	abstract run(): void;

	protected getPosition(): number[] {
		return [10, 20];
	}
}

class Dog extends Animal {
	name: string
	constructor(name:string){
		super();
		this.name = name;
	}
	run(): void {
		console.log(this.name + 'run fast');
	}
}

let dog = new Dog('dog');
dog.run();
```

---

### interface

接口是是一种规范/约束；
接口内的属性和方法需要实现 (除了不必):

###### 对象接口

```
interface UserInter {
	name: string;
	show?(): void;
	[key: string]: any;	// 可以有额外任何属性 值是any类型
}

let obj: UserInter = {
	name: "tom",
	age: 10,
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

    接口可以定义函数的参数和返回的类型

```
interface Good {
	(price: number): number;
}

let handle: Good = (price: number) => price * 2;
```

```
interface UserInter {
	name: string;
	age: number;
	isLocked: boolean;
}

const user: UserInter = {
	name: "tom",
	age: 10,
	isLocked: false,
};

// 对形参约束，对返回值约束
function handleLock(user:UserInter, lock: boolean):UserInter{
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

	constructor(user:UserInter){
		this._userInfo = user;
	}
	get userInfo(): UserInter {
		return this._userInfo;
	}
}
```

###### 接口继承

-   继承相同接口名

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
	say();
}
interface IRun {
	run();
}

// interface IMix extends ISay, IRun {}

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
	run() {
		console.log("我可以跑10公里");
	}
}

new Person("tom", "k");
```

### 泛型

动态传递类型

```
// function dump(arg: string): string {
// 	return arg;
// }

// function dump(arg: number): number {
// 	return arg;
// }

function dump<T>(arg: T): T {
	return arg;
}

dump<string>('hello');
dump<number>(1);
dump(true);	 	// 不传自动推断
```

###### 泛型约束

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
