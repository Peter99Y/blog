---
title: vuex & pinia
---

### Pinia
状态管理工具，支持vue3组合式API
getter使用computed函数
移除了mutation
在action中支持同步异步操作

```
import { defineStore } from "pinia";
import { computed, ref } from "vue";

export const useCounterStore = defineStore("counter", () => {
	// 定义数据（state）
	const count = ref(0);

	// 定义getter
	const doubleCount = computed(() => {
		return count.value * 2;
	});

	// 定义修改数据的方法（action 同步/异步)
	const increment = () => {
		count.value++;
	};
	const list = ref([]);
	const getList = () => {
		setTimeout(() => {
			list.value = [{ id: 1}, { id: 2, }];
		}, 1000);
	};

	// 以对象的方式return供组件使用
	return {
		count,
		doubleCount,
		increment,
		getList,
		list,
	};
});
```

```
<script setup>
import { onMounted } from "vue";
import { useCounterStore } from "../stores/counter";
import { storeToRefs } from "pinia";

const counterStore = useCounterStore();
// 直接结构导致响应式失效，使用storeToRefs
// const { count } = counterStore;

const { count, doubleCount, list } = storeToRefs(counterStore);
const { increment, getList } = counterStore;

onMounted(() => {
    getList();
});
</script>

<template>
    <button @click="increment">{{ count }}</button>

    <div>{{ doubleCount }}</div>

    <ul>
        <li v-for="it of list" :key="it.id">{{ it.id }}</li>
    </ul>
</template>
```
---


### Vuex
实现组件全局状态(数据)管理的一种机制，方便实现组件之间数据的共享.
存储在vuex中的数据都是响应式的，能够实时保持数据与页面的同步.
只有组件之间共享的数据，才有必要存储到vuex中，对于组件中的私有数据，存储在组件自己的data中即可。
install: npm install vuex --save
######State: 提供唯一的公共数据源，所有共享的数据都要统一放在store.state对象中
######Mutation: 变更Store中state的数据只能使用mustation, 不可以直接修改state的数据, 可以集中监控所有数据的变化
######Actions: 通过在action中触发mutation里的方式进行异步操作数据. 必须通过action，异步操作不能直接在mutation中!
######Getter: 不会修改state中的数据，只是包装数据的作用，类似computed&data的关系
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