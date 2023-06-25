---
title: vue3组合式API
---

```
<script>
export default {
    setup(){
        const msg = '这是一条信息';

        const handleMsg = ()=>{
            console.log('这是一条信息');
        }
        return {
            msg,
            handleMsg
        }
    }
}
</script>

<template>
    <div>{{ msg }}</div>

    <button @click="handleMsg">click</button>
</template>
```

语法糖简写模式

```
<script setup>
const msg = "这是一条信息";

const handleMsg = () => {
    console.log("这是一条信息");
};
</script>
```

### reactive

```
<script setup>
// 接受一个对象参数，并返回响应式对象
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

-   接收一个基本数据类型/引用数据类型，并返回一个响应式对象

```
<script setup>
// ref函数内部依赖reactive函数实现；
import { ref } from "vue";

const count = ref(2);
const handleCount = () => {
    count.value++;
};

// 接收一个泛型；
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

// return基于相应式数据做计算的值
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
        immediate: true,
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

#### 声明周期

组合式 API setup 中码字 等于 选项式 API beforeCreate/created 声明周期中码字, 其他生命周期都是加了一个前缀 on;

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

#### 父子通信

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

defineProps 接收父级传递的参数

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

#### 子父通信

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

defineEmits 定义自定义事件

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

#### provide & inject

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
