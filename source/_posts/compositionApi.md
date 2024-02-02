---
title: vue3组合式API
---

## setup

```
<template>
    <div>{{ data.count }}</div>
    <button @click="handleChange">click</button>
</template>

<script>
import {reactive} from 'vue'
export default {
    setup() {
        const data = reactive({
            count: 1,
        });

        const handleChange = ()=>{
            data.count += 1;
        }

        return {
            data,
            handleChange,
        }
    },
};
</script>
```

语法糖简写模式

```
<template>
    <div>{{ data.count }}</div>
    <button @click="handleChange">click</button>
</template>

<script setup>
import { reactive } from "vue";

const data = reactive({
    count: 1,
});

const handleChange = () => {
    data.count += 1;
};
</script>
```

## reactive

接受一个引用类型，不接受基本数据类型，并返回深层响应式对象

```
<script setup>
import { reactive } from "vue";

const state = reactive({ count: 1 });

const handleCount = () => {
    state.count++;
};
</script>

<template>
    <div>{{ state.count }}</div>
    <button @click="handleCount">click</button>
</template>
```

注意：不能对 reactive 生成的响应式对象覆盖，否则会丢失响应式

```
import { reactive } from "vue";

let list = reactive<string[]>([]);

setTimeout(() => {
  list = ['hello', 'world'];
  console.log('list', list);  // ['hello', 'world']; 视图不刷新
}, 2000);
```

#### shallowReactive

只对根属性(第一层级)有效;

```
import { shallowReactive } from "vue";

let user = shallowReactive({
  code: 200,
  data: {
    city: 'Shanghai',
  }
})

setTimeout(() => {
  user.code = 201;

  // user.data.city = 'Beijing';    // 无效
  user.data = { city: 'Beijing' };
}, 2000);
```

## ref

###### 获取 DOM 元素；

```
<script setup>
import { ref, onMounted } from "vue";
import Child from "./components/Right.vue";

const inputRef = ref(null);
const childRef = ref(null);

onMounted(() => {
    console.log(inputRef.value);    // input
    childRef.value.changeMsg();     // 调用子组件方法
});
</script>

<template>
    <input ref="inputRef" type="text" />
    <Child ref="childRef" />
</template>
```

###### 接收一个基本/引用数据类型，声明响应式状态返回一个深层次响应式对象；

```
<script setup>
import { ref } from "vue";

// new Proxy({value: 'tom'}); 会包装成一个代理对象，绑定的value属性就是响应式对象；
// 所以js部分需要.value才能修改，html不用；
const user = ref('tom');

const count = ref(2);

const handleCount = () => {
    count.value++;
};
</script>

<template>
    <div>{{ count }}</div>
    <button @click="handleCount">click</button>
</template>
```

###### 标注类型

类型推断

```
const user = ref({name: "tom"});      // const user: Ref<{name: string }>
```

Ref type;

```
import type { Ref } from 'vue';

const user: Ref = ref({ name: 'tom', age: 10 })
```

泛型

```
import { ref } from "vue";

type userType = {
  name: string;
  age: number;
};

const user = ref<userType>({
  name: "tom",
  age: 10,
});
```

#### shallowRef & triggerRef

shallowRef: 返回浅层响应数据;
triggerRef: 强制触发 shallowRef;

```
import { shallowRef, triggerRef } from "vue";

const user = shallowRef({ name: 'tom' })
user.value.name = "jerry";

if (user.value.name === 'jerry') {
  console.log(true);            // true;   value对象下的数据会改变, 但视图不刷新
  triggerRef(user);             // 强制出发一个shallowRef
}

// user.value = { name: 'jerry' };   // 只能直接改变value对象，视图才会刷新
```

#### customRef

自定义一个 ref

```
import { customRef } from "vue";

function fnRef<T>(param: T) {
  return customRef((track, trigger) => {
    return {
      get() {
        track();    // 收集依赖
        return param;
      },
      set(val) {
        param = val;
        trigger();  // 触发依赖更新
      }
    }
  })
}

const str = fnRef<string>('hello');

const handleClick = () => {
  str.value = 'world';
}
```

#### toRef & toRefs & toRaw

提取出响应式对象的一个属性, 将其转换成 ref 响应式对象, 可同源修改 (传入响应式对象有效, 非响应式对象无效)

```
import { reactive, toRef } from "vue";

let user = reactive({
	age: 10,
	data: {
		info: {
			username: 'tom',
		}
	}
});
let username = toRef(user.data.info, 'username');
let age = toRef(user, 'age');

setTimeout(() => {
	age.value ++;      // 11
	user.age ++;       // 12
	username.value = 'jerry';	// jerry
}, 1000);
```

toRefs

```
import { reactive, toRefs } from "vue";

let user = reactive({ name: 'tom', age: 10 });
const { name, age } = toRefs(user);

setTimeout(() => {
  name.value = 'jerry';

  // age.value = '11';      // err; Ref<number> 自动类型推断
  age.value = 11;
}, 1000);
```

toRaw 将响应式对象转成普通对象

#### defineExpose

默认不开放给父组件访问，通过 defineExpose 暴露组件内的属性和方法

```
<script setup>
import { ref } from "vue";
const msg = ref("");

const changeMsg = () => {
    msg.value = "hello word";
};

defineExpose({
    msg,
    changeMsg,
});
</script>

<template>
    {{ msg }}
</template>
```

## computed

-   传入函数

-   传入{get,set}对象

```
<script setup>
import { ref, computed } from "vue";

const list = ref([1,2,3,4,5]);

const computedList = computed(()=>{
    return list.value.filter(it=>it > 2);
})
</script>
```

## watch

#### 单个数据侦听

```
const count = ref(0);

watch(
    count,
    (newV, oldV) => {
        console.log("count:", newV);
    },
    {
        immediate: true,    // 页面第一次加载触发;
        deep: true,         // 深度监听, reactive无需;
        flush: 'pre',       // 默认pre 组件更新前执行，sync同步执行，post组件更新后执行
    }
);
```

#### 多个数据侦听

```
watch(
  [count, number],
  ([newCount, newNumber], [oldC, oldN]) => {
    console.log("count:", newCount, oldC);
    console.log("number:", newNumber, oldN);
  }
);
```

#### 精确侦听

deep 是对一个引用类型所有属性的监听;
而 watch 传入回调函数是对引用类型的单个属性深度监听;

```
<script setup>
const result = ref({
  info: {
    count: {
      number: 0,
    }
  },
});

watch(
  () => result.value.info.count.number,
  (newV) => {
    console.log(newV);
  }
)

const handleCount = () => {
  result.value.info.count.number++;
};
```

#### watchEffect

-   回调函数内每个涉及的数据的修改都会触发;
-   首次加载立即执行（普通 watch 需要配置 immediate）
-   返回停止监听函数
```
import { watchEffect, ref } from "vue";

let username = ref('')
let password = ref('')

const stop = watchEffect((onBefore) => {
  onBefore(() => {
    console.log('before trigger')
  });

  console.log('after trigger', username.value);
  console.log('after trigger', password.value);
})

const stopWatch = () => stop();
```

---

## 组合式 Api 生命周期

组合式 API setup 中码字 等于 选项式 API beforeCreate/created 声明周期中码字, 其他生命周期都是加了一个前缀 on;
vue2 | vue3
--- | ---
beforeCreate | setup
created | setup
beforeMount | onBeforeMount
mounted | onMounted
beforeUpdate | onBeforeUpdate
updated | onUpdated
beforeDestory | onBeforeUnmount
destroyed | onUnmounted

```
<script setup>

import { onMounted } from "vue";

onMounted(()=>{
    console.log(1)
})

onMounted(()=>{
    console.log(2)
})
</script>
```

## directive

任何以 v 开头的驼峰式命名的变量都可以被用作一个自定义指令
```
<script>
export default {
    directives:{
        focus:{
            mounted(el){    // 在绑定的元素挂载完成后调用，只在第一次插入DOM时；DOM更新时不触发
                el.focus();
            },
            updated(el){
                el.focus(); // 每次DOM更新时都会触发updated函数
            }
        }
    },
    
    // 简写 (如mounted 和 updated函数中逻辑相同)
    directives: {
        focus: (el) => {
            el.focus();
        },
        color: (el, binding) => {
            el.style.color = binding.value;
        },
    },
    data() {
        return {};
    },
};
</script>
```

## 动态组件&lt;composite&gt;

```
<script setup>
import Foo from "./Foo.vue";
import Bar from "./Bar.vue";
</script>
I
<template>
    <component :is="Foo" />
    <component :is="someCondition ? Foo : Bar" />
</template>
```

## defineProps 父子通信

```
<script setup>
import { ref } from 'vue';

// 组合式API下无需注册
import Child from './components/Right.vue'

const count = ref(100);
</script>

<template>
    <Child :count="count"/>
</template>
```

```
<script setup>
defineProps({
    count: Number,
});
</script>

<template>
    <div>{{ count }}</div>
</template>
```

## defineEmits 子父通信

```
<script setup>
import { ref } from "vue";
import Child from "./components/Right.vue";

const info = ref("o");

const handleMsg = (msg) => {
    info.value = msg;
};
</script>

<template>
    {{ info }}
    <Child @pushMsg="handleMsg" />
</template>
```

```
<script setup>
const $emit = defineEmits(["push-msg"]);

const handleMsg = () => {
    $emit("push-msg", "hello");
};
</script>

<template>
    <button @click="handleMsg">click</button>
</template>
```

## another props & emits

特别恶心，选项式和组合式请别混用；

```
<template>
    <div>{{ newCount }}</div>
    <button @click="handleChange">add</button>
</template>

<script>
import { computed } from "vue";
export default {
    props: {
        count: "",
    },
    emits: ["change-count"],
    setup(props, { emit }) {
        let newCount = computed(() => props.count * 2);

        let handleChange = () => {
            emit("change-count", newCount.value);
        };

        return {
            newCount,
            handleChange,
        };
    },
};
</script>
```

## provide & inject

多层嵌套组件中使用；

`父组件.vue`

```
<script setup>
import { ref, provide } from "vue";
import Child from "./components/Right.vue";

const msg = ref("hello");
const handleMsg = () => {
    msg.value += 1;
};

// 传递数据
provide("info", msg);

// 传递方法
provide("setInfo", handleMsg);
</script>

<template>
    <Child ref="childRef" />
</template>
```

`子组件.vue`

```
<script setup>
import { inject } from "vue";

const receiveInfo = inject("info");

const handleMsg = inject('setInfo')
</script>

<template>
    <div>{{ receiveInfo }}</div>
    <button @click="handleMsg">click</button>
</template>
```
