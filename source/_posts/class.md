---
title: class
---

## 编程思想

### 面向过程编程

类似于人的思维，将问题分解为多个步骤，然后按照步骤进行解决。

```javascript
const form = document.querySelector("form");
const username = document.querySelector("#username");
const password = document.querySelector("#password");

function submitHandler(e) {
  e.preventDefault();
  const usernameValue = username.value;
  const passwordValue = password.value;

  if (usernameValue.trim() === "" || passwordValue.trim() === "") {
    alert("用户名或密码不能为空");
    return;
  }

  const userData = { username: usernameValue, password: passwordValue };
}

form.addEventListener("submit", submitHandler);
```

### 函数式编程 (FP)

```javascript
const REQUIRED = "REQUIRED";
const MIN_LENGTH = "MIN_LENGTH";

function validate(value, flag, validatorValue) {
  // 必须填写
  if (flag === REQUIRED) return value.trim().length > 0;
  // 最小长度
  if (flag === MIN_LENGTH) return value.trim().length > validatorValue;
  // 可扩展其他...
}

// 获取表单的值
function getUserInput(inputId) {
  return document.querySelector(`#${inputId}`).value;
}

function createUser(username, password) {
  if (!validate(username, REQUIRED) || !validate(password, MIN_LENGTH, 5)) {
    throw new Error("用户名或密码格式不正确");
  }
  return { username, password };
}

function greetUser(user) {
  console.log(`用户 ${user.username} 登录成功`);
}

function submitHandler(e) {
  e.preventDefault();

  try {
    const username = getUserInput("username");
    const password = getUserInput("password");

    const user = createUser(username, password);
    greetUser(user);
  } catch (err) {
    alert(err.message);
  }
}

function createForm(formId, handler) {
  const form = document.getElementById(formId);
  form.addEventListener("submit", handler);
}

// 解耦：把用户输入的逻辑从业务逻辑中抽离出来，可给多个表单复用，如登录表单、注册表单、修改密码表单等等
createForm("login-form", submitHandler);
```

### 面向对象编程 (OOP)

```javascript
class Validator {
  // 定义静态属性和方法
  static REQUIRED = "REQUIRED";
  static MIN_LENGTH = "MIN_LENGTH";
  static validate(value, flag, validatorValue) {
    if (flag === this.REQUIRED) return value.trim().length > 0;
    if (flag === this.MIN_LENGTH) return value.trim().length > validatorValue;
  }
}

class User {
  constructor(username, password) {
    this.username = username;
    this.password = password;
  }
  greet() {
    console.log("用户" + this.username + "登录成功");
  }
}

class UserInputForm {
  constructor() {
    this.form = document.getElementById("login-form");
    this.username = document.getElementById("username");
    this.password = document.getElementById("password");

    // 给form表单元素添加事件监听；
    // addEventListener第二个参数是回调函数，回调中的this指向的是当前DOM元素；
    // 修改this指向为当前类实例；
    this.form.addEventListener("submit", this.submitHandler.bind(this));
  }
  submitHandler(e) {
    e.preventDefault();

    // 获取表单数据
    const usernameValue = this.username.value;
    const passwordValue = this.password.value;

    // 验证表单数据
    if (
      !Validator.validate(usernameValue, Validator.REQUIRED) ||
      !Validator.validate(passwordValue, Validator.MIN_LENGTH, 6)
    ) {
      alert("用户名或密码格式不正确");
      return;
    }

    // 创建新的用户对象
    const User = new User(usernameValue, passwordValue);
    user.greet(); // 登录成功
  }
}

new UserInputForm();
```

类是一个抽象性的模板，对于该类事物的综合描述，这些描述中分为两种内容，一是属性，二是方法；

### consturctor

**是类的构造函数，用于传递参数，返回实例对象**，通过 new 命令生成对象实例时，**默认执行**的方法，可以理解为初始化的方法。如没有定义，类内部自动创建一个 constructor;

### static

像工具类无需实例化多个对象（订单/顾客/产品类 等这种需要实例化很多对象），就可以使用 static 定义类的静态属性和方法；

> 注意： 1.类没有声明提升； 2.类中的方法都默认开启了严格模式

```javascript
class Animal {
  // 静态属性
  static paws = 4;

  constructor(name) {
    // 实例对象共有属性
    this.name = name;
    // 创建实例对象就立即调用
    this.init();
  }

  init() {
    console.log("initial finished");
  }

  // 实例对象共有方法
  run(method) {
    console.log(this.name, `is runing ${method}`);
  }

  // 静态方法
  static eat() {
    console.log("chips");
  }
}

console.log(Animal.paws); // 4

// 生成实例对象
var dog = new Animal("dog");
var cat = new Animal("cat");

console.log(dog.name); // dog
console.log(cat.name); // cat

dog.run("fast");
cat.run("slow");
```

等价于 ES5 构造函数

```javascript
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

- 子类可以继承父类的方法和属性；
- 继承后遵循就近原则，最后调用**父类普通函数**；
- super 关键字用于访问和调用父类上的函数。可以调用父类的构造函数和普通函数；

```javascript
class Father {
  run() {
    console.log("run");
  }
  say() {
    console.log("f");
  }
}

class Son extends Father {
  say() {
    console.log("s");
  }
}

var s = new Son(1, 2);
s.run(); // run
s.say(); // s
```

```javascript
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

class Son extends Father {
  constructor(x, y) {
    // 调用父类constructor构造函数; 等价于 Father.constructor(x,y);
    super(x, y);
  }
  say() {
    super.say(); // 调用父类普通函数;
  }
}

var s = new Son(1, 2);
s.sum(); // 3
s.say(); // fuck
```

分析：

1. new Son(1,2)传给的是 son.contrutor(x,y);
2. s.sum()调用，是 father.sum 函数, 而 sum 函数里的 this.x 指是 father.constructor.x; 所以没访问到就报错；
3. 利用 super(x,y)调用父类构造函数 等价于调用 father.constructor(x,y), 并传递形参;
4. 最后 s.sum()调用，sum(){}函数中 this.x 就能访问到 father 自身上的 x 属性了；

---

```javascript
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
s.sum(); // 5
s.minus(); // 1
```

##### 注意

```javascript
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
dog.run(); // 这里dog.run调用，run函数里的this, 指向dog实例对象;
```

#### example

```javascript
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
      this.deleteIcon[i].onclick = this.removeTab.bind(
        this.deleteIcon[i],
        this,
      );
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
