---
title: vuex & pinia
---

### Pinia

-   支持完整的 ts；
-   支持 vue3 组合式 API；
-   action 中支持同步异步操作；
-   移除了 mutation，直接在 action 中修改 state 中的值；
-   轻量化，压缩后的体积只有 1kb；

`name.ts`

```
export const enum Names {
	COUNTER = "COUNTER",
}
```

`index.ts`

```
import { defineStore } from "pinia";
import { computed, ref } from "vue";
import { Names } from "./name";

type User = {
	name: string;
	age: number;
};
const Login = (): Promise<User> => {
	return new Promise((resolve) => {
		setTimeout(() => {
			resolve({
				name: "Tom",
				age: 100,
			});
		}, 2000);
	});
};

export const useCounterStore = defineStore(Names.COUNTER, {
        state: () => {
		return {
			current: 1,
			name: "tom",
			user: <User>{},
		};
	},
	getters: {
		doubleCount: (state) => state.current * 2,
                tripleCount(): number {
			return this.current * 3;
		},
	},
	actions: {
		increment(data: number) {
			this.current = data || this.current + 1;
		},
		async setUser() {
			const result = await Login();
			this.user = result;
			this.setName(result.name);
		},
		setName(name: string) {
			this.name = name;
		},
	},
});
```

```
<template>
	<div>
		<h4>姓名：{{ testStore.name }}</h4>
		<h4>年龄：{{ testStore.current }}</h4>
		<h4>双倍：{{ testStore.doubleCount }}</h4>
		<h4>三倍：{{ testStore.tripleCount }}</h4>

		<button @click="testStore.increment">click</button>
	</div>
</template>
<script setup>
import { useTestStore } from "@/store";
const testStore = useTestStore();
</script>
```

注意：如直接解构，会导致响应式失效，需使用 storeToRefs，返回refs对象;

```
<script setup>
import { useTestStore } from "@/store";
import { storeToRefs } from "pinia";
const testStore = useTestStore();

// const { current } = counterStore;
const { current, name } = storeToRefs(testStore);
const handleChange = () => {
	current.value++;
};
</script>
```

###### 修改 state 方式

$patch、$reset、$subscribe
```
<script setup>
import { useTestStore } from "@/store";
const testStore = useTestStore();

// 监听state改变
testStore.$subscribe((args, state)=>{
    console.log('args', args)
    console.log('state', state)
})

// 监听actions的函数触发
testStore.$onAction((args)=>{
    console.log('args', args)
})

const handleChange = () => {
    // actions中的函数去修改
    testStore.increment(100);

	// 直接修改
	// testStore.current += 1;

	// 批量修改
	// testStore.$patch({
	// 	current: 100,
	// 	name: "jerry",
	// });

    // 批量修改函数模式
	// testStore.$patch((state) => {
	// 	state.current = 100;
	// 	state.name = "Jerry";
	// });

    // $state模式
    // testStore.$state = {
        // current: 100,
        // name: 'Jerry',
    // }

    // 恢复初始值
	testStore.$reset();
};
</script>
```

---

### Vuex

实现组件全局状态(数据)管理的一种机制，方便实现组件之间数据的共享.
存储在 vuex 中的数据都是响应式的，能够实时保持数据与页面的同步.
只有组件之间共享的数据，才有必要存储到 vuex 中，对于组件中的私有数据，存储在组件自己的 data 中即可。
install: npm install vuex --save

###### State: 提供唯一的公共数据源，所有共享的数据都要统一放在 store.state 对象中

###### Mutation: 变更 Store 中 state 的数据只能使用 mustation, 不可以直接修改 state 的数据, 可以集中监控所有数据的变化

###### Actions: 通过在 action 中触发 mutation 里的方式进行异步操作数据. 必须通过 action，异步操作不能直接在 mutation 中!

###### Getter: 不会修改 state 中的数据，只是包装数据的作用，类似 computed&data 的关系

```
import Vue from 'vue';
import Vuex from 'vuex';

Vue.use(Vuex);

export default new Vuex.Store({
    state:{
        count:0,
    },
    mutations:{
        addCount(state, val){
            state.count+=val;
        }
    },
    actions:{
        addAsync(context, val){
            setTimeout(() => {
                context.commit('addCount', val)
            }, 1000);
        }
    },
    getters:{
        showNum(state){
            return '当前最新的数量是:'+state.count;
        }
    }
})
```

```
<template>
    <div class="plus">
        <!---
        第一种访问组件State中数据的方式
        <div>当前count值: {{$store.state.count}} </div>

        <br/>

        第二种通过导入的mapState函数，将当前组件需要的全局数据，映射为当前组件的computed计算属性
        --->
        <div>当前count值: {{count}} </div>
        <button @click="add">+1</button>

        <br/>

        <button @click="handleAction">异步add+1</button>

        <div>第一种获取getter方式: {{$store.getters.showNum}}</div>
        <div>第二种通过mapGetters方式: {{showNum}}</div>

    </div>
</template>

<script>
import { mapState, mapMutations,mapActions,mapGetters } from 'vuex';
export default {
    name: 'plus',
    computed:{
        ...mapState(['count']),
        ...mapGetters(['showNum'])
    },
    methods:{
        ...mapMutations(['addCount']),  //通过导入的mapMutations函数，将需要的mutations函数，映射为当前组件的methods的方法

        add(){
            //  this.$store.commit('addCount', 1);  //第一种调用mutations里的方法
            this.addCount(2)    //第二种调用mutations里的方法
        },

        ...mapActions(['addAsync']),
        handleAction(){
            //this.$store.dispatch('addAsync', 1) //第一种触发actions的方式
            this.addAsync(1)    //第二种触发actions里的方法，通过导入的mapActions函数，将需要的actions函数映射为当前组件的methos方法
        }
    }
}
</script>
```

```
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import store from './store/store'

Vue.config.productionTip = false

new Vue({
  router,
  store,
  render: h => h(App)
}).$mount('#app')

```
