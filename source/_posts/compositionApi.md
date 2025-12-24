---
title: vue3
---

## vue3 与 vue2 区别

1.  vue2 选项式写法，在 data 中定义的属性，methods，computed，watch 中都会使用，造成代码结构是分散不相关的；组合式 api 则可以统一写在一块，高内聚；

2.  选项式与 ts 结合并不友好，组合式与 ts 结合更友好，比如类型推断；

3.  组合式函数
    但 vue3 组合式 api 也造成一个问题，就是它不像 vue2 这种强制的帮你划分了结构，methods 里的方法都在 methods 对象里面，computed 里的方法都在 computed 里面，以及 watch、filter 等等。也就造成了 vue3 代码最后写出来很乱，所有的东西都在一块；解决方案就是拆分业务，根据业务把同一个组件中独立的业务代码抽象封装成函数。一个页面每一块的业务都可以独立成一个函数导入调用。 - 按照业务声明以“use”开头的逻辑函数； - 把独立的业务逻辑封装到各个函数内部； - 函数内部把组件中需要用到的数据和方法以对象的形式 return 出去； - 在组件中导入函数，把对应的数据或方法解构使用即可；

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

```TYPESCRIPT
<template>
    <Hello ref="child" />
</template>

<script lang="ts" setup>
import { ref } from 'vue';
import Hello from '@/components/HelloWorld.vue'

let child = ref<InstanceType<typeof Hello>>()
console.log(child.value?.changeMsg);
</script>
```

```TYPESCRIPT
<script setup lang="ts">
let msg = 'hello';

const changeMsg = () => {
  msg = "hello word";
};

defineExpose({
  msg,
  changeMsg
})
</script>
```

## computed

- 传入函数

- 传入{get,set}对象

```
<script setup>
import { ref, computed } from "vue";

const list = ref([1,2,3,4,5]);

const computedList = computed(()=>{
    return list.value.filter(it => it > 2);
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

- 回调函数内每个涉及的数据的修改都会触发;
- 首次加载立即执行（普通 watch 需要配置 immediate）
- 返回停止监听函数

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

## v-model

```
<template>
    <Child v-model:modelValue="count" />

    <!-- 等价于 -->

    <Child v-model="count" />
</template>
```

```
<template>
	<button @click="$emit('update:modelValue', modelValue + 1)">
		{{ modelValue }}
	</button>

	<input
		type="text"
		@input="(e) => $emit('update:msgValue', e.target.value)"
	/>
</template>

<!-- 选项式 -->
<script>
export default {
	props: ["modelValue"],
	emits: ["update:modelValue"], // 更新哪个props的属性
};
</script>

<!-- 组合式 -->
<script setup>
defineProps({ modelValue: Number, msgValue: String });

defineEmits(["update:modelValue", "update:msgValue"]);
</script>
```

###### v-model 修饰符

```
<template>
    <Child v-model.upper="count" />
    <Child v-model:title.upper.substr="name" />
</template>
```

```
<template>
    <!-- <input type="text" :value="modelValue" @input="change" /> -->
    <input type="text" :value="title" @input="change" />
</template>

<script>
export default {
    props: [
        // "modelValue",
        // "modelModifiers",  // 来源 v-model
        "title",
        "titleModifiers",     // 来源 v-model:title => 'titleModifiers';
    ],
    emits: [
        // "update:modelValue",
        "update:title",
    ],
    created() {
        console.log(this.titleModifiers);
    },
    methods: {
        change($event) {
            let value = $event.target.value;

            if (this.titleModifiers.upper) {
                value = value.toUpperCase();
            }

            if (this.titleModifiers.substr) {
                value = value.substr(0, 3);
            }
            this.$emit("update:title", value);
        },
    },
};
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

## &lt;component&gt;

动态组件

```
<template>
    <div v-for="(it, idx) of 2" :key="idx" @click="handleSwitch(idx)">{{ it }}</div>

    <component :is="whichOne"></component>
</template>

<script lang="ts" setup>
import { shallowRef } from 'vue';
import Hello from '@/components/HelloWorld.vue'
import World from '@/components/World.vue'

// 使用shallowRef，无需代理劫持组件属性，只需要代理到whichOne.value最外层对象;
const whichOne = shallowRef(Hello);

const handleSwitch = (index: number) => {
    if (index === 0) {
        whichOne.value = Hello;
    } else {
        whichOne.value = World;
    }
}

</script>
```

## defineProps

```
<script setup lang="ts">
const props = defineProps({
  title: String,
  arr: Array,
})

// ts泛型字面量
const props = defineProps<{
  title: string,
  arr: number[],
}>()

// defineProps没有给props提供默认值的方式，
// 使用ts特有的withDefaults函数设置默认值
const props = withDefaults(
  defineProps<{
    title: string,
    arr: number[],
  }>(),
  {
    title: 'hello',
    arr: () => [1, 2, 3]
  }
)

console.log(props.title)  // 'hello'
</script>

<template>
  <h2>{{ title }}</h2>
  <h2>{{ arr }}</h2>
</template>
```

## defineEmits

```
<script setup>
import { ref } from "vue";
import Child from "./components/Right.vue";

const info = ref("o");

const handleMsg = (msg: string) => {
    info.value = msg;
};
</script>

<template>
    {{ info }}
    <Child @pushMsg="handleMsg" />
</template>
```

```
<script setup lang="ts">
const $emit = defineEmits(["push-msg", "push-data"]);

// ts
const $emit = defineEmits<{
  (e: 'push-msg', data: string): void
  (e: 'push-data', data: string): void
}>();


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

provide 在父组件中提供给嵌套的后代组件数据和方法，在任何一级后代组件中都可以使用 inject 接收；(非后代组件无法共享哦)

- 注意：会同源修改，若不希望后代组件修改，provide 使用 readonly;

```
<script lang="ts" setup>
import { ref, provide, readonly } from 'vue';

const colorVal = ref<string>('red');

provide('color', colorVal);
provide('color', readonly(colorVal));

const handleSwitch = () => {
    colorVal.value = 'blue';
}
</script>
```

```
<script lang="ts" setup>
import { inject } from 'vue';

const color = inject('color');

const handleChange = () => {
  color.value = 'pink';
}
</script>
```

## mitt

兄弟组件传参
`npm install mitt -S`;

main.ts 初始化

```
import mitt from "mitt";
const Mit = mitt();

declare module "vue" {
	export interface ComponentCustomProperties {
		$bus: typeof Mit;
	}
}

app.config.globalProperties.$bus = Mit;
```

```
<script lang="ts" setup>
import { getCurrentInstance } from 'vue';
const instance = getCurrentInstance();

const change = () => {
  instance?.proxy?.$bus.emit('on-hello', '你好');
  instance?.proxy?.$bus.emit('on-more', '你好');
}
</script>
```

```
<script setup lang="ts">
import { getCurrentInstance } from 'vue';

const instance = getCurrentInstance();
const getData = (data:any)=>{
  console.log(data)
}
instance?.proxy?.$bus.on('on-hello', getData);
instance?.proxy?.$bus.off('on-hello', getData);
instance?.proxy?.$bus.all.clear();

instance?.proxy?.$bus.on('*', (type, data) => {
  console.log(type);
  if(type === 'on-hello'){
    console.log(data)
  }

  if(type === 'on-more'){
    console.log(data)
  }
})
</script>
```

## defineAsyncComponent

异步组件加载，按需加载

```
<script>
import { defineAsyncComponent } from "vue";
import LoadingComponent from '../components/loding.vue'
import ErrorComponent from '../components/error.vue'

export default {
    components: {
        Son1: defineAsyncComponent(()=>import('../components/HelloWorld.vue'))
        Son2: defineAsyncComponent({
            // 加载函数
            loader: () => import("../components/HelloWorld.vue"),

            // 加载异步组件时使用的组件
            loadingComponent: LoadingComponent,
            // 展示加载组件前的延迟时间，默认为 200ms
            delay: 200,

            // 加载失败后展示的组件
            errorComponent: ErrorComponent,
            // 如果提供了一个 timeout 时间限制，并超时了
            // 也会显示这里配置的报错组件，默认值是：Infinity
            timeout: 3000,
        }),
    },
};
</script>
```

###### suspense

suspense 内置组件协调对异步依赖的处理, 它让组件树上层等待下层的多个嵌套异步依赖项解析完成，在等待时渲染一个加载状态。

1. 可以等待的异步依赖异步组件
2. 可以等待的异步依赖带有异步 setup() 钩子的组件。这也包含了使用 &lt;script setup&gt; 时有顶层 await 表达式的组件。

async.vue

```
<template>
    <div class="async">
        <div class="async-content">
            <div><img :src="data.url"></div>
            <div class="async-pop">
                <div>姓名：{{ data.name }}</div>
                <div>年龄：{{ data.age }}</div>
                <div>{{ data.desc }}</div>
            </div>
        </div>
    </div>
</template>

<script setup lang="ts">
import { axios } from '@/server/axios'

interface Data {
    data: {
        name: string,
        age: number,
        url: string,
        desc: string
    }
}

/**
 * 使用顶层await将组件变成异步组件
 *
    await 必须在 async 函数中使用，如果直接在一个模块最外层直接使用 await，使得整个模块看起来就像是一个 async 函数;
    await Promise.resolve();

    注意：顶层 await 仅仅是允许在模块最外层使用 await，非 async 函数不能直接使用；
 */


const { data } = await axios.get<Data>('./data.json')
</script>
```

```
<template>
    <div>
        <!-- suspense协调对异步依赖的处理, 它让组件树上层等待下层的多个嵌套异步依赖项解析完成，在等待时渲染一个加载状态。 -->
        <Suspense>
            <!-- 默认插槽展示的组件 -->
            <template #default>
                <Async />
            </template>

            <!-- 反馈插槽 设置异步加载时动作，如骨架屏和loading-->
            <template #fallback>
                <el-skeleton :rows="2" />
            </template>
        </Suspense>
    </div>
</template>

<script lang="ts" setup>
import { defineAsyncComponent } from 'vue';

// import Async from '@/components/async.vue'   // 同步导入
const Async = defineAsyncComponent(() => import('@/components/async.vue')) // 异步导入

</script>
```

## teleport

将一个组件内部的一部分 html 模板渲染到该组件的 DOM 结构外层的位置去，比如 body 和 vue 应用平级，使其不受父级样式的影响；

## keep-alive

可以使被包含的组件保留状态避免重新渲染
当组件被包裹了 keep-alive 后，组件内会新增 onActivated, onDeactivated 两个声明周期函数。

```html son.vue
<template>
  <input type="text" v-model="name" />
</template>

<script lang="ts" setup>
  import { onActivated, onDeactivated, onMounted, onUnmounted, ref } from "vue";

  const name = ref(null);

  onActivated(() => {
    console.log(
      "keep-alive 1.组件首次挂载触发; 2.每次切换从缓存中被重新插入时执行;"
    );
  });

  onDeactivated(() => {
    console.log(
      "keep-alive 1.组件组件卸载触发; 2.每次切换从DOM 上移除、进入缓存执行"
    );
  });

  onMounted(() => {
    console.log("1.组件首次挂载触发;");
  });

  onUnmounted(() => {
    console.log("1.组件卸载触发;");
  });

  watch(
    () => name.value,
    (val) => {
      console.log("注意：watch函数无法触发");
    }
  );
</script>
```

```html father.vue
<keep-alive v-if="needKeepAlive === true">
  <Hello v-if="isShown"></Hello>
  <World v-else></World>
</keep-alive>
```

```javascript router.js
{
   path: '/profile',
   name: 'Profile',
   component: Profile,
   meta:{
        keepAlive: false // 在路由中设置组件是否缓存
   }
},
```

```html app.vue
<keep-alive v-if="$route.meta.keepAlive">
  <router-view />
</keep-alive>
```

## transition

```
<template>
    <button @click="handleSwitch">click</button>

    <transition name="test">
        <div class="box" v-if="isShown"></div>
    </transition>
</template>

<script lang="ts" setup>
import { ref } from 'vue';
const isShown = ref(true);

const handleSwitch = () => {
    isShown.value = !isShown.value;
}
</script>

<style scoped lang="scss">
.box {
    width: 200px;
    height: 200px;
    background-color: orange;
}

// 动画进入时
.test-enter-from {
    width: 0;
    height: 0;
}
// 动画效果
.test-enter-active {
    transition: all 0.2s ease;
}
/**
    动画结束最后一帧效果
    可不写，或与原始box属性保持一致，否则最后一帧效果非常跳脱和突兀；
*/
// .test-enter-to {
//     width: 500px;
//     height: 500px;
// }


/**
    动画开始第一帧效果
    可不写，或与原始box属性保持一致，否则第一帧效果非常跳脱和突兀；
*/
// .test-leave-from {
//     width: 200px;
//     height: 200px;
// }
.test-leave-active {
    transition: all 0.2s ease;
}
.test-leave-to {
    width: 0;
    height: 0;
}
</style>
```

## tsx

`npm install @vitejs/plugin-vue-jsx -D`

注意：ref 在 template 中会自动读取.value，tsx 需要手动指定.value;

```
// 第一种方式：直接返回默认的渲染函数
//  export default function (){
//     return (<div>hello world</div>)
//  }

// 第二种方式：optionsApi (少)
// import { defineComponent } from "vue";
// export default defineComponent({
//     data(){
//         return {
//             name: 'tom',
//         }
//     },
//     render (){
//         return (<div>{this.name}</div>)
//     }
// })

// 第三种方式：setup
import { defineComponent, ref } from "vue";
export default defineComponent({
	setup() {
        // 注意：ref在template中会自动读取.value，tsx需要手动指定.value;
		const flag = ref(false);
		return () => <div v-show={flag.value}>hello</div>;
	},
});
```

```
interface propsInt {
	name: string;
}

const A = (_, { slots }) => (
	// <div>{slots.default ? slots.default() : "默认值"}</div>
	<div>{slots.foo?.()}</div>
);

import { defineComponent, ref } from "vue";
export default defineComponent({
	props: {
		name: String,
	},
	emits: ["on-change"],
	setup(props: propsInt, { emit }) {
		const flag = ref(false);

		const list = [1, 2, 3];

		const handleChange = (str: string) => {
			emit("on-change", str);
		};

		const slot = {
			default: () => <div>这是默认插槽</div>,
			foo: () => <div>这是具名插槽</div>,
		};

        const searchName = ref<string>('');

		return () => (
			<div>
				<div v-show={flag.value}> hello </div>

				{/* v-if 无效，三元，与，非代替; */}
				<div> {flag.value ? <span>真</span> : <span>假</span>} </div>

				{/* v-for 无效，map 方法代替; */}
				{/* v-bind 无效，花括号代替; */}
				<div>
					{list.map((it, idx) => {
						return <span data-index={idx}>{it}</span>;
					})}
				</div>

				<div>{props.name}</div>

				<button onClick={() => handleChange(searchName.value)}>click</button>

				<A v-slots={slot}></A>

                <input type="text" v-model={searchName.value} />
                {searchName.value}
			</div>
		);
	},
});
```

## directive

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

## style 新特性

###### 插槽选择器

修改从父组件插槽传递的 DOM 样式

```
<template>
    <Hello>
        <div class="content">这是内容</div>
    </Hello>
</template>
```

```
<template>
	<slot></slot>
</template>

<style scoped>
/*
.content{
	color: orange;
}
*/

:slotted(.content) {
	color: orange;
}
</style>
```

###### 全局选择器

```
<style scoped>
:global(.content){
	color: orange;
}
</style>

```

###### 动态 css 属性

```
<script setup>
import { ref } from "vue";
// const style = ref("red");

const style = ref({
	backgroundColor: "red",
});
</script>

<style scoped>
.box {
	/* background-color: v-bind(style); */
	background-color: v-bind("style.backgroundColor");
}
</style>
```

###### module

```
<template>

	<div class="box">1</div>

	<div :class="$style.box">1</div>

	<div :class="[$style.box, $style.content]">1</div>
</template>

<style scoped module>
.box {
	background-color: orange;
}
.content{
    color: green;
}
</style>
```

```
<template>
	<div :class="test.box">1</div>

	<div :class="cssObj.box">1</div>
</template>

<script setup>
import { useCssModule } from "vue";
const cssObj = useCssModule("test");
</script>

<!-- 自定义module名称 -->
<style scoped module="test">
.box {
	background-color: orange;
}
</style>
```

## vite 环境变量

普通 script 中可以从 import.meta.env 读取当前运行的环境

```
BASE_URL: '/', // 默认是vite.config.ts中defineConfig函数对象的base值
MODE: 'development',
DEV: 'true', // 运行开发环境run dev为真
PROD: 'false', // 运行生产环境run build为真（永远与DEV相反）
```

###### 环境加载 .env 文件

根文件可创建 .env.development、 .env.test、.env.production 等文件

###### 自定义环境变量名称

必须以 VITE\_开头才能暴露给 import.meta.env 对象上

`.env.production`

```
VITE_HTTP = https://production.com
```

`package.json`

```
  "scripts": {
    "prod": "vite --mode production", // 与创建的.env.production文件名称一致；
  },
```

###### http-server

打开打包后的 index.html 文件报错跨域， 借助 http-server 开启服务预览生产环境；
`npm install http-server -g`;

`http-server -p 9000 dist`; // 开启一个端口号 9000 的服务

## 组合式函数

```
// banner.vue

<script setup>
import { useBanner } from "./components/useBanner";
const { bannerList, guessRef, onScrollToLower } = useBanner();
</script>

<template>
    <RabbitGuess ref="guessRef" @click="onScrollToLower"/>
	<div v-for="it of bannerList">{{ it.name }}</div>
</template>
```

```
// useBanner.ts

import { onMounted, ref } from "vue";
import { getBannerAPI } from "@/apis/home";
import type { RabbitGuessInstance } from '@/types/component'

export function useBanner() {
    const bannerList = ref([]);
    const guessRef = ref<RabbitGuessInstance>()

    const getBanner = async () => {
        const res = await getBannerAPI({ site: 2 });
        bannerList.value = res.result;
    };

    const onScrollToLower = () => {
        guessRef.value?.getMore()
    }

    onMounted(() => getBanner());

    return {
        guessRef,
        bannerList,
        onScrollToLower,
    };
}
```
