---
title: "Ts常见错误"
---

###### not assignable to type 'never[]'

```
import type { Ref } from 'vue';
const cartList = ref([]);       // 数组未定义类型，此时是 never[] 类型；
const cartList = ref([] as CartItem[]); // 采用断言方式

const getData = async () => {
  const res = await getMemberCartApi()  // api处理过返回的是 CartItem[] 结构的数组;
  cartList.value = res.result;      // err; Type 'CartItem[]' is not assignable to type 'never[]'
}
```

###### v-model 无法绑定变量

```
const profile = ref<ProfileDetail>();   // err; 只做了类型标注，但没有默认值，此时v-model没有绑定到属性 而报错;

const profile = ref<ProfileDetail>({}); // err; 设置空对象，此时类型报错，因为{}对象不满足 ProfileDetail类型;

const profile = ref<ProfileDetail>({} as ProfileDetail);     // 断言空对象结构为ProfileDetail类型;

const profile = ref({} as ProfileDetail);   // 断言后可不用泛型约束同样有提示;


<input
    class="input"
    v-model="profile.nickname"      // err; 设置ref<ProfileDetail>()时, '__VLS_ctx.profile' is possibly 'undefined';
/>
```

###### void is not assignable to type '() => void'

```
let fn: () => void;

function myFunction(): void {
    // do something
}

fn = myFunction();  // err; Type 'void' is not assignable to type '() => void';

fn = myFunction;  // 赋值这个函数 而非执行这个函数;


or ---------------------------------------------


let result: void;

function myFunction(): void {
    // do something
}

result = myFunction();
```

###### 1

```
let obj = { username: "tom" };
// let key = "username";   // 可变的变量作为obj的索引是不固定的，而对象的这个索引是固定的；

const key = "username";
let n = obj[key];   // err;
```

###### 1

```
let obj: object = { username: "tom" };

const key = "username";
let n = obj[key];   // err; 'username' can't be used to index type '{}'

// obj: object ={ username: "tom" } 相当于 obj: {} = { username: "tom" };
// 这里 obj[key] 实际上是从约束的object类型上获取属性，而这个对象是没有的；
```
