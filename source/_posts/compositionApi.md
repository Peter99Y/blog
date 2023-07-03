---
title: vue3组合式API
---

### setup

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

### reactive

接受一个对象参数，不接受基本数据类型，并返回深层响应式对象

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

### ref

-   获取 DOM 元素；

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

-   接收一个基本数据类型/引用数据类型，并返回一个深层响应式对象

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

type UserType = {
    name: string;
};
let user = ref<UserType>({ name: "tom" });
const change = () => {
    user.value.name = "jack";
};
</script>

<template>
    <div>{{ count }}</div>
    <button @click="handleCount">click</button>
</template>
```

###### toRef, toRefs

```
<template>
    <h5>{{ name }}</h5>
    <div>{{ count }}</div>
    <div>{{ city }}</div>
    <button @click="handleChange">click</button>
</template>

<script>
import {reactive, toRef, toRefs, ref} from 'vue'
export default {
    setup() {
        const city = ref('北京');
        const data = reactive({
            count: 1,
            name: 'tom',
            city: city
        });

        let count = toRef(data, 'count');
        let allData = toRefs(data);

        const handleChange = ()=>{
            // data.count += 1;
            // count.value += 1;
            allData.count.value += 1;
        }

        return {
            // 非响应式
            // count: data.count,

            // toRef: 响应式的，同时原数据修改/自身修改，同时都会修改；
            // count: count,

            // toRefs:
            ...allData,

            handleChange,
        }
    },
};
</script>
```

###### defineExpose

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

### computed

```
<script setup>
import { ref, computed } from "vue";

const list = ref([1,2,3,4,5]);

const computedList = computed(()=>{
    return list.value.filter(it=>it > 2);
})
</script>
```

### watch

##### 单个数据侦听

```
const count = ref(0);

watch(
    count,
    (newV, oldV) => {
        console.log("count:", newV);
    },
    {
        immediate: true,    // 页面第一次加载触发；
        // deep: true
    }
);
```

##### 多个数据侦听

```
watch([count, name], ([newC, newN], [oldC, oldN]) => {
    console.log("count:", newC, "name:", newN);
});
```

##### 精确侦听

```
<script setup>
import { ref, watch } from "vue";

const info = ref({
    count: 0,
    msg: 'hello'
});

const handleCount = () => {
    info.value.count++;
};

watch(()=> info.value.count, (newV)=>{
    console.log('info.count:', newV);
})

</script>
```

##### watchEffect

-   页面首次加载会立即执行；
-   自动侦听内部的依赖并重新执行；
-   第二个参数可选 DOM 渲染前后才执行；

---

#### 组合式 Api 生命周期

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

#### directive

任何以 v 开头的驼峰式命名的变量都可以被用作一个自定义指令

#### 动态组件&lt;composite&gt;

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

#### defineProps 父子通信

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

#### defineEmits 子父通信

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

#### another props & emits

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

#### provide & inject

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

### vue 中使用 ts

其他请参考官网

#### reactive

```
<script setup lang="ts">
interface IUser {
    name: string
}
const user : IUser = reactive({
    name: 'tom'
})
</script>
```

#### ref

```
<script setup lang="ts">
import type {Ref} from 'vue';
const user : Ref<string> = ref('tom');

// or

const user = ref<string>('tom');
</script>
```

```
<script setup lang="ts">
import { ref, onMounted } from 'vue'

const el = ref<HTMLInputElement | null>(null)

onMounted(() => {
  el.value?.focus()
})
</script>

<template>
  <input ref="el" />
</template>
```

#### computed

```
<script setup lang="ts">
const double = computed<number>(() => {
  // 若返回值不是 number 类型则会报错
})
</script>
```

#### 函数

```
<script setup lang="ts">
function handleChange(event) {
  // `event` 隐式地标注为 `any` 类型
  console.log(event.target.value)
}
</script>

<template>
  <input type="text" @change="handleChange" />
</template>
```
