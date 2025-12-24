---
title: Vue
---

### MVVM

M(model:指的是 data 中的数据) V(view: 视图) VM(ViewModel: 实现控制逻辑,将 M & V 结合在一起)

![](/images/vue2&3/1.webp)

```
<style type="text/css">
    	[v-cloak]{
    		display: none;
    	}
</style>

<div id="app">
      隐藏未编译的标签直到实例准备完毕。否则会在之前显示双花括号
	  <div v-cloak>{{msg}}</div>

      填充纯文本
	  <div v-text="msg"></div>

      填充HTML片段
	  <div v-html="msghtml"></div>

      跳过编辑过程, 直接填充原始信息--如双花括号
	  <div v-pre>{{msg}}</div>

</div>

<script>

<script>
	var timer = null;
	new Vue({
		el:'#app',

		data:{
			msg:'hello',
			msghtml:'<h1> <a>wold</a> </h1>'
		},
		methods:{

		}
	})
</script>
```

###### key: 作用给节点做唯一标识，避免 Vue 的虚拟 DOM 和 diff 算法将节点或组件复用；

如: 在表单元素相同时，即使切换了也会被复用，需添加 key 属性；在 for 循环时，需给节点添加 key

```
<div id="app">

	<button @click="changeUser">切换账号</button>

	<div v-if="isUser">
		<input type="text" name="username" placeholder="请输入用户账号" v-model="userAccount" key="account">
	</div>
	<div v-else>
		<input type="text" name="username" placeholder="请输入用户邮箱" v-model="userAccount" key="email">
	</div>

</div>
<script>
	new Vue({
		el:'#app',
		data:{
			isUser:true,
			userAccount:'',
		},
		methods:{
			changeUser(){
				this.isUser = !this.isUser;
			}
		}
	})
</script>
```

###### class & style

```
<div id="app">

	<div :class="{ active: isShow }"></div>
    <div :class="['back-color',  isShow ? 'active': '' ]"></div>


	<div :style="setStyle"></div>
	<div :style="styleObj"></div>
	<div :style="[ setStyle, styleObj ]"></div>

</div>
<script>
	new Vue({
		el:'#app',
		data:{
			setStyle:'border: 1px solid #ccc',

			styleObj:{
				'background-color': 'orange',
				border: '3px solid #000'
			}
		},
	})
</script>
```

-   动态切换 class

```
<div id="app">

	<ul>
		<li v-for="(item,index) in list" :class="[currentIndex === index? 'active':'']" @click="change(index)">{{item}}</li>
	</ul>

</div>
<script>
	new Vue({
		el:'#app',
		data:{
			currentIndex: 0,
			list: ['新闻', '热点', '国际']
		},
		methods:{
			change(index){
				this.currentIndex = index;
			}
		}
	})
</script>
```

### v-on

```
<div id="app">
    事件修饰符
    <a href="www.baidu.com" @click.prevent="goAnother">link</a>

    <!-- 取消冒泡: 点击button时， 如btn的click事件不加.stop修饰符，btn & div都会执行；button加上，就只有button执行 -->
	<div @click="handleF">
		<button @click.stop="handleBtn">btn</button>
	</div>

        <!-- 利用冒泡 点击所有btn，都湖执行handleBtn-->
	<div @click="handleBtn">
		<button>btn1</button>
		<button>btn2</button>
		<button>btn3</button>
	</div>

    按键修饰符
    <input type="text" name="" @keyup.enter="submit">
    //自定义按键修饰符
    <input type="text" name="" @keyup.f2="submit">
</div>

<script>
    Vue.config.keyCodes.f2 = 113;
	new Vue({
		el:'#app',
		methods:{
            goAnother(){
				console.log('取消默认跳转，而是转为编程式跳转')
			},
			handleBtn(){
				console.log('btn');
			},
			handleF(){
				console.log('father')
			}
		}
	})
```

### v-bind & v-on

v-bind 绑定属性/对象中所有键值对
v-on 绑定事件/对象中所有事件键值对

```
    <!-- data - { key: 'maxlength' } -->

    <MyInput
        :id="1"
        :[key]="100"
        :type="'text'"
        :size="'small'"
        :placeholder="'请输入'"
        v-model="search"
        @focus="handleFocus"
        @input="(val)=> search = val.replace(/[^\d]/g, '')"
        @blur="handleBlur"
        >hello</MyInput
    >


    <MyInput
        v-bind="{
            id: 1,
            type: 'text',
            size: 'small',
            placeholder: '请输入',
        }"
        >hello</MyInput
    >
```

```
<el-input v-bind="$attrs" v-on="$listeners"></el-input>
```

### props & $attrs & $listener

-   给组件标签上添加 class 和 style 元素，默认会添加到组件的根标签上(一个根标签，vue3 中多个根标签不会默认添加)
-   $attrs 获取组件标签上，除了 props 已经声明接收过的属性的对象；
-   $listener 获取组件标签上绑定的所有事件的对象；

`父组件.vue`

```
<template>
    <Left
        class="ccc mmm"
        data-index="99"
        style="border:1px solid #ccc"
        :click="show"
        @show-model="handleShow"
    />
</template>

<script>
import Left from "./components/Left.vue";

export default {
    components: { Left },
    methods: {
        show(){
            alert('可向子组件传递方法!');
        },
        handleShow(){
            console.log('这是子组件的事件，只是父组件穿透');
        }
    },
};
</script>
```

`子组件.vue`

```
<template>
    <h2 :class="$attrs.class">1</h2>
    <h4 :style="$attrs.style">4</h4>
    <h6>{{ $attrs["data-index"] }}</h6>

    <button @click="click">click</button>
</template>

<script>
export default {
    inheritAttrs: false,    // 此属性可阻止属性自定添加到根组件上(只有一个根组件时)
    props: {
        dataIndex: "",      // 如props接收了，$attrs中将获取不到；
        click: Function,
    }
};
</script>
```

---

### 动态组件

vue 提供了一个内置的<component>组件，专门用来实现组件动态渲染

-   <component>是组件的占位符
-   is 属性动态指定要渲染的组件名称

```
<div id="app">
	<ul>
		<li @click="change( 'Home' )">上学期</li>
		<li @click="change( 'List' )">下学期</li>
	</ul>

	<div :is="current" />
    or
    <component is="current"/>
</div>

<script>
	new Vue({
		el:'#app',
		components:{
			Home:{ template: `<div>Home组件</div>` },
			List:{ template: `<div>List组件</div>` },
		},
		data:{
			current: 'Home',
		},
		methods:{
			change(val){
				this.current = val;
			}
		}
	})
</script>
```

### directive 自定义指令

```
<div id="app">

	<button v-color=" 'green' ">click</button>
	<button v-color="{ want: 'yellow'}">click</button>

	<input type="text" name="" v-focus>

</div>
```

-   全局自定义指令

```
Vue.directive('color', {
	//bind：初始化设置。 只调用一次,指令第一次绑定到元素时调用；
	bind: (el, binding) => {
		// el: 指令所绑定的DOM元素;
		// binding: {rawName: 'v-color', name: 'color', value: 'green'}
		el.style.background = binding.value
	},
	inserted: (el) => {         // inserted: 当被绑定的元素插入到DOM时调用
		el.focus();             // el: 指令所绑定的元素，可直接用来操作DOM;
	}
})
```

-   组件局部自定义指令

```
new Vue({
	el: '#app',
	data: {
		setColor: 'red',
	},
	directives: {
		focus: {
			inserted: (el) => {
				el.focus();
			},
			componentUpdated: (el) => {  // DOM元素及其子元素全部更新后调用
				el.focus();
			},
		}
	}
})
```

###### 全局挂载

```
// main.js 引入插件并注册
import { lazyPlugin } from "./directives/index";
```

```
export const lazyPlugin = {
	install(app/vue) {
        // 防抖
        Vue.directive("debounce", {
            inserted: function (el, binding) {
                let timer;
                el.addEventListener("keyup", () => {
                    if (timer) {
                        clearTimeout(timer);
                    }
                    timer = setTimeout(() => {
                        binding.value();
                    }, 400);
                });
            },
        });
    },
};
```

---

### computed

-   computed & methods 区别:
    computed - 基于依赖进行缓存的，多次调用如果依赖的数据没有改变，只会执行一次；
    methods - 调用几次就会执行几次；

```
<div id="app">

	{{setMsg}}

    <div v-for="item in 3">
		<p> {{ setMsg2() }} </p>
	</div>

    <el-date-picker
       v-model="chooseTime"
       type="daterange"
       value-format="yyyy-MM-dd"
       range-separator="至"
    >
</div>
```

```
<script>
	new Vue({
		el:'#app',
		data:{
			msg:'extra',
            queryParams:{
                startTime: '',
                endTime: '',
            }
		},
		computed:{
			setMsg:function(){
				return this.msg.substring(0,1).toUpperCase() + this.msg.substring(1);
			},
            // 也可以设置对象(get & set)
            chooseTime: {
              get() {
                  return [this.queryParams.startTime, this.queryParams.endTime];
              },
              set(range) {
                  const [start, end] = range || ["", ""];
                  this.queryParams.startTime = start;
                  this.queryParams.endTime = end;
                },
            },
		},
		methods:{
			setMsg2:function(){
				return this.msg.substring(0,1).toUpperCase() + this.msg.substring(1);
			}
		}
	})
</script>
```

---

### watch

```
export default {
    data() {
        return {
            msg: "hello",

            msgObj: {
                name: "tom",
                age: 10,
            },
        };
    },
    watch: {
        msg(newV, oldV) {
            console.log("newV");
        },
        msg: {
            handler: function (newV, oldV) {},
            immediate: true, // 组件初始化不会掉用watch，如果想立即掉用...
        },
        msgObj: {
            handler: function (newV, oldV) {},
            deep: true, // 监听对象/数组 每一个值的变化
        },
        "msgObj.name": {
            //监听具体到对象的单个属性变化
            handler: function (newV, oldV) {},
        },
    },
}
```

### filter

注意: 过滤器内无法获取 this(也就是 Vue 实例对象), 只支持对被过滤的数据的改变;
注意: vue3 移除了 filter，可使用方法或计算属性完成；

```
<div id="app">

	<div> {{sex | sexF}} </div>
	<div :id=" sex | sexF"></div>

</div>
<script>
	// Vue.filter('sexF',function(value){         //全局过滤器
	// 	return value == 1? 'male':'female'
	// })

	new Vue({
		el:'#app',
		data:{
			sex:1,
		},
		filters:{		                          //局部过滤器
			sexF(value){
                // console.log(this.sex);  undefined
				return value == 1? 'male':'female'
			}
		}
	})
</script>
```

---

### 全局组件注册

```
<div id="app">

	<son-component :title="msg" />

</div>
<script>
    // 这里是html文件中通过cdn引入vue方式
	Vue.component('son-componennt',{
		template:`<button>son</button>`
	})

	new Vue({
		el:'#app',
		data:{
			msg: 'hello',
		},
	})
</script>
```

在 vue 脚手架项目 main.js 中注册

```
import GlobalCom from './components/GlobalCom.vue'

app.component(GlobalCom.name, GlobalCom);  // 第一个参数组件名，第二个是具体渲染的组件
```

---

### 局部组件注册

在 vue 页面中引入子组件，并在 components 对象中注册

```
<sonComp @change="handleChange"/>


import sonCom from "./components/sonCom.vue";

export default {
    components: {
        sonCom,
    },
    methods: {},
};
```

---

### 组件传值

###### 父组件向子组件传值

通过在子组件标签上自定义属性名和值传递

```
<template>
    <sonCom title="100"/>
</template>
```

`sonCom.vue`

```
<template>
    <div>{{title}}</div>     // 100
</template>

<script>
export default {
    props:{
      title:[String]
    },
}
</script>
```

---

###### $emit: 触发自定义事件，子组件向父组件传值

`子组件.vue`

```
<template>
    <button type="button" @click="trigger">click</button>
    <button @click="$emit('click')">click</button>
</template>

<script>
export default {
    data(){
        return {
            count: 100
        }
    },
    // emits: ["click", 'changeCount'], // vue3中需要在子组件中声明自定义事件名称
    emits: {
        click: null,
        changeCount: null,
        handleDelete(val) {                 // 事件参数验证
            if (/^\d+$/.test(val)) {
                return true;
            }
            throw Error("delete 事件需要数值参数");
        },
    },
    methods:{
        trigger(){
            this.$emit('changeCount', this.count);
        }
    }
}
</script>
```

`父组件.vue`

```
<template>
    <Left @click="show" @changeCount="show"/>
</template>

<script>
import Left from "./components/Left.vue";
export default {
    components: { Left },
    methods: {
        changeCount(count){
            console.log('传递的count ', count)
        },
        show(count){
            console.log('传递的count ', count);
        }
    },
};
</script>
```

---

###### v-model

`v-model实现原理:`

```
<input v-model="msg">

<!-- 等价于 -->

<input type="text" :value="msg" @input="msg = $event.target.value" />
```

---

组件上使用 v-model 指令

```
<!-- 父组件.vue -->
<template>
    <Child :value="count" @change="count = $event" />
</template>

<!-- 子组件 -->
<template>
	<button @click="$emit('change', value + 1)">{{ value }}</button>
</template>

<script>
export default {
	props: ["value"],
};
</script>
```

```
<!-- 父组件.vue -->
<template>
	<Child v-model="count" />
</template>

<!-- 子组件 -->
<template>
	<button @click="$emit('input', value + 1)">{{ value }}</button>
</template>

<script>
export default {
	props: ["value"],
};
</script>
```

---

###### vue2 中央事件总线 eventbus

vue3 移除，可借助 mitt 第三方包创建 eventBus 对象;安装 mitt;

`eventBus.js`

```
import mitt from 'mitt';
const bus = mitt();
export default bus;
```

`数据发送方.vue`

```
<script>
import bus from "../util/eventBus";

export default {
    methods: {
        trigger() {
            // emit()触发自定义事件，发送数据
            bus.emit("countChange", 100);
        },
    },
};
</script>
```

`数据接收方.vue`

```
<script>
import bus from "../util/eventBus";

export default {
    data() {
        return {
            count: 0,
        };
    },
    created() {
        // on()方法注册一个自定义事件，接收数据
        bus.on("countChange", (data) => {
            this.count = data;
        });
    },
};
```

共享数据方式 1.父子，2.子父，3.v-model，4.eventbus, 5.provide&inject, 6.vuex

---

### vue2 生命周期函数

-   created: 组件在内存中创建完成。
-   mounted: 组件第一次被渲染完成。
-   updated: 组件的 data 数据更新后，vue 会重新渲染组件的 DOM 结构，完成后自动掉用 updated 生命周期函数。
-   unmounted: 组件被销毁完成。

---

### $refs

ref 用来获取 DOM 元素或组件实例对象。每个 vue 组件实例上都包含了$refs 对象，存储了 ref 引用的 DOM 元素/组件实例。

```
<template>
    <div>
        <Left ref="left" />
        <h2 ref="h2">hello</h2>
        <input ref="inp" type="text">

        <button @click="trigger">click</button>
    </div>
</template>


<script>
import Left from './components/Left.vue';

export default {
    components:{
        Left
    },
    data() {
        return {
            count: 100,
        };
    },

    methods: {
        trigger() {
            // 可操作dom元素
            this.$refs.h2.style.color = 'red';
            this.$refs.inp.focus();

            // 可调用子组件methods方法
            this.$refs.left.init();
        },
    },
};
</script>
```

---

### $nextTick(cb)

此方法会把 cb 回调函数推迟到下一个 DOM 更新结束之后执行。DOM 是异步地渲染完成后，从而保证回调函数可以获得最新的 DOM 元素；
$nextTick 返回的是一个 promise 对象，所以可以使用 async&await 语法糖；

```
<template>
    <div ref="div">{{ msg }}</div>

    <button @click="trigger">click</button>
</template>
```

```
<script>
export default {
    data(){
        return {
            msg: 'hello'
        }
    },
    methods: {
        trigger(){
            this.msg = 'hello word';
            console.log(this.$refs.div.innerHTML); // hello;  DOM还未渲染完成;

            this.$nextTick(()=>{
                console.log(this.$refs.div.innerHTML); // hello word;
            })
        },
    },
};
</script>
```

---

### $set

Vue 会在初始化实例时对 property 执行 getter/setter 转化，所以 property 必须在 data 对象上存在才能让 Vue 将它转换为响应式的；

```
var vm = new Vue({
  data:{
    a:1,
    formData:{
        name: ''
    },
  }
})
```

对于已经创建的实例，Vue 不允许动态添加根级别的响应式 property。但是，可以使用 Vue.set(object, propertyName, value) 方法向嵌套对象添加响应式 property。
`Vue.set(vm.formData, 'password', 123)`

还可以使用 vm.$set 实例方法，这也是全局 Vue.set 方法的别名;

`this.$set(this.formData,'password', 123)`

---

### transition 过渡

transition 标签包裹 router-view or 状态切换的标签 才能实现过度动效!!!

v-enter / v-leave: 进入/离开过度的开始状态;
v-enter-to / v-leave-to: 进度/离开过度的结束状态;
v-enter-active / v-leave-active: 进入/离开过度时的生效状态;

提供了以上 6 个类名 or 使用 name 自定义 transition

```
<style type="text/css">
	.cc-enter-active,  .cc-leave-active{  /* 进入 / 离开 过度生效状态 */
		transition: all .5s;
	}
	.cc-enter,  .cc-leave-to{             /* 进入状态 & 结束状态 opacity为0 */
		opacity: 0;
		transform: translateX(10px);
	}
</style>
<div id="app">

	<button @click="isShow = !isShow">click</button>

	 <transition name="cc">      <!---使用过度自定义name--->
		 <div v-show="isShow">11</div>
	 </transition>
</div>
```

```
<style type="text/css">

	.v-enter{							/*过度进入时初始状态*/
		opacity: 0;
		transform: translateX(100px);
	}
	.v-enter-to, .v-leave{  			/*进入过度结束状态 & 过度结束离开初始状态*/
		opacity: 1;
		transform: translateX(0px);
	}

	.v-leave-to{						/*过度结束状态*/
		opacity: 0;
		transform: translateX(-100px);
	}
	.v-enter-active,.v-leave-active{	/*过度动效*/
		transition: all .2s ease;
	}
	p{
		position: absolute;    /* 解决当translateX 进入和离开时的状态 朝同一方向移动时，会发生该节点从Dom树上删除而导致重绘 发生闪跳*/
	}
</style>
<div id="app">

	<button @click="isShow = !isShow">click</button>

	<div>
	  	<transition>
	    		<p key="1" v-if="isShow">Lorem ipsum dolor sit amet.</p>
	    		<p key="2" v-else>no data</p>
	  	</transition>
  	</div>
</div>
```

---

### 动画

```
<style type="text/css">
	.sss-enter-active {
	  animation: mmm .5s;
	}
	.sss-leave-active {
	  animation: mmm .5s reverse;  /*reverse 进入时0%到100%,离开时100%到0%*/
	}
	@keyframes mmm {
	  0% {
	    transform: scale(0);
	  }
	  50% {
	    transform: scale(1.5);
	  }
	  100% {
	    transform: scale(1);
	  }
	}
    div{
		max-width: 200px;
	}
</style>
<div id="app">

	<button @click="isShow = !isShow">click</button>

	<div>
	  	<transition name="sss">
	    		<p v-if="isShow">Lorem ipsum dolor sit amet.</p>
	  	</transition>
  	</div>
</div>
```

### 自定义过度类名 & animate.css 第三方动画插件结合使用

```
<div id="app">

	<button @click="isShow = !isShow">click</button>

	<transition
		enter-active-class="animated bounce"            //进入时动效
		leave-active-class="animated bounceOutRight">	//离开时动效

		<p v-show="isShow">22222</p>

	</transition>
</div>
```

```
<style type="text/css">

	.sss-enter-active {
	  animation: mmm .2s;
	}
	.sss-leave-active {
	  animation: mmm .2s reverse;
	}
	@keyframes mmm {
		0% {
		    opacity: 0;
		    transform: translateX(100px);
		}
		100% {
		    opacity: 1;
		    transform: translateX(0px);
		}
	}
	div{
		max-width: 200px;
	}
</style>
<div id="app">

	<button @click="isShow = !isShow">click</button>

	<div>
	  	<transition name="sss" mode="out-in">  //不添加mode就是同时
	    		<p key="1" v-if="isShow">Lorem ipsum dolor sit amet.</p>
	    		<p key="2" v-else>no data</p>
	  	</transition>
  	</div>
</div>
```

---

```
<style type="text/css">

	.sss-enter-active {
	  animation: sta .2s;
	}
	.sss-leave-active {
	  animation: lea .2s;
	}
	@keyframes sta {
		0% {
		    opacity: 0;
		    transform: translateX(100px);
		}
		100% {
		    opacity: 1;
		    transform: translateX(0px);
		}
	}
	@keyframes lea {
		0% {
		    opacity: 1;
		    transform: translateX(0px);
		}
		100% {
		    opacity: 0;
		    transform: translateX(-100px);
		}
	}
	div{
		max-width: 200px;
		position: relative;
	}
	p{
		position: absolute;
	}
</style>
<div id="app">

	<button @click="isShow = !isShow">click</button>

	<div>
	  	<transition name="sss">
	    		<p key="1" v-if="isShow">Lorem ipsum dolor sit amet.</p>
	    		<p key="2" v-else>no data</p>
	  	</transition>
  	</div>
</div>
```

---

### 路由: 对于单页面应用程序来说，主要通过不同的 hash 地址，实现不同页面的切换，称作前端路由

1.URL 中 hash #(井号)代表网页中的一个位置，不会发起新的 http 请求，只是实现客户端页面的定位。
2.#右边的字符，是位置的标识符。 3.改变#会改变浏览器的访问历史记录
4.window.location.hash 可读取或修改#值
5.onhashchange 时间，监听#值，如发生改变就会触发该事件

```
<template>
  <div>
      <a href="#/aaa">跳a</a>
      <a href="#/bbb">跳b</a>

      <component :is="comName"/>
  </div>
</template>

<script>
  // 注册两个全局组件
  Vue.component('com1',{
        template:`<button>com1</button>`
  })
  Vue.component('com2',{
        template:`<button>com2</button>`
  })

  new Vue({
        el:'#app',
        data:{
            comName:'com1' // 默认显示组件
        },
        mounted(){
            window.onhashchange = ()=>{
                let hash = window.location.hash;
                switch(hash){
                    case '#/aaa':
                       return this.comname = 'com1';
                    case '#/bbb':
                        return this.comname = 'com2';
                }
            }
        },
    })
</script>
```

> SPA 单页面应用 (Single Page Application)  
> 整个 web 网站只有一个 HTML 页面，所有组件展示/切换都在者一个页面内完成。不同组件之间切换通过前端路由实现。

> 前端路由工作方式：用户点击来页面上的路由链接，导致 URL 地址栏中的 Hash 值发生了变化，前端路由监听到了 Hash 变化后，把对应的组件渲染到浏览器中。
> 1.URL 中 hash #代表网页中的一个位置，不会发起新的 http 请求，只是实现客户端页面的定位。
> 2.#右边的字符，是位置的标识符。 3.改变#会改变浏览器的访问历史记录。
> 4.window.location.hash 可读取或修改#值。
> 5.window.onhashchange 事件监听#值，如发生改变就会触发该事件

-   vue-router 3.X 是 vue2 才能使用，4.X 是 vue3 使用！
-   router-link：路由链接；
-   router-view 路由占位符：路由匹配到的组件都会被渲染到 router-view 所在的位置；
-   .router-link-active：默认 router-link 的选中项

`声明式跳转`

```
<template>
    <div>
        <router-link to="/left"> left </router-link>
        <router-link :to="'/right/' + 12992"> 路由跳转 </router-link>
        <router-link :to="{name:'hot', params:{Rid: 23233} }"> 命名路由跳转 </router-link>

        <router-view />
    </div>
</template>
```

`编程式跳转`

```
    methods: {
        jump() {
            this.$router.push(`/right/${12992}`);

            this.$router.push({
                path: "/right",
                query: {
                    id: 111,
                },
            });
            // 命名路由跳转
            this.$router.push({
                name: "right",
                params: { id: 23233 },
            });

            // 后退 or 前进
            this.$router.go(-1);

            // 跳转下一个路由，并删除跳转前一个路由的history记录
		    this.$router.replace(`/right/${12992}`)
        },
    },
```

---

-   vue-router 路由重定向；
-   vue-router 嵌套路由；
-   vue-router 路由传参；
-   vue-router 命名路由；

`router.js`

```
import { createRouter, createWebHashHistory } from "vue-router";

import Left from "../components/Left.vue";
import Right from "../components/Right.vue";
import Tab1 from "../components/tab1.vue";
import Tab2 from "../components/tab2.vue";

const router = createRouter({
	history: createWebHashHistory(),
	routes: [
		// 路由重定向：访问某个地址时，强制用户跳转到另外的地址
		{ path: "/", redirect: "/left" },
		{ path: "/left", component: Left },
		{
            // 动态路由
			path: "/right/:id",
            // 命名路由：必须是唯一
            name: 'right',
			component: Right,
            // 嵌套路由
			children: [
				{ path: "tab1", component: Tab1 },
				{ path: "tab2", component: Tab2 },
			],
		},
	],
});
export default router;
```

---

###### 路由懒加载

根据匹配到的路由按需加载组件，而不是应用打开时加载所有的组件

```
import Vue from 'vue'
import Router from 'vue-router'
//import Msite from '../pages/Msite/Msite.vue'
export default new Router({
  routes: [
       { path: '/search',
         name: 'Search',
         components:()=>import('../../page/confirm.vue'),
       }
  ]
```

###### 路由守卫

文档: `https://router.vuejs.org/zh/`
全局前置守卫 router.beforeEach

```
const routes = [
  { path:'/', redirect:'/login' },
  { path: '/login', name: 'login', component: Login },
  {
    path: '/home',
    name: 'home',
    component: Home,
    redirect:"/welcome",
    meta:['add'],
    children:[ { path:'welcome', component:welcome } ]
  },
  //当用户自己输入非网站内的路由 显示notFound页面
  { path:'*', component:notFound },
]

const router = new VueRouter({ routes });

// to目标路由对象；from正要离开的路由对象；next允许跳转；
router.beforeEach((to,from,next)=>{

  if(to.path === '/login'){
      next();
  }else{
      const token = sessionStorage.getItem('token');
      if(!token){
          next('/login')    // 跳转到login让用户登录
      }else{
          next();
      }
  }
})
```

###### 组件内守卫

```
beforeRouteEnter(to, from, next){
    if(from.path === '/import'){
        next(vm=>{       //组件实例还未创建，无法获取this, 通过vm获取vue实例对象
            vm.msg = '来自/import'
        })
    }else{
        next();
    }
},
beforeRouteLeave (to, from, next) {
    //离开守卫通常用来禁止用户在还未保存修改前突然离开。该导航可以通过 next(false) 来取消。
    const answer = window.confirm('Do you really want to leave? you have unsaved changes!')
    if (answer) {    //this可用
        next()
    } else {
        next(false)
    }
}
methods:{},
```

###### ES6 模块化:

模块化就是把单独的一个功能封装到一个模块(文件)中，模块之间相互隔离，但是可以通过特定的接口公开内部成员，也可以依赖别的模块；这样方便代码的重用，从而提升开发效率，方便后期维护;

ES6 模块化定义: 1.每个 JS 文件都是一个独立的模块 2.导入模块成员 import 3.暴露模块成员 export;

> 扩展：babel 是一个语法转换工具，可以把高级的，有兼容性的 js 代码转换为低级的、没有兼容性的 js 代码；

---

###### proxy 反向代理

-   在开发阶段调试接口时可以使用代理，项目上线后依旧需要服务器开启 Cors 跨越资源请求。
-   在根目录中创建 vue.config.js 配置文件，和 package.json 同级. 如果项目的根目录中存在这个文件，它会被自动加载。
-   如果前端应用和后端 API 服务器没有运行在同一个主机上，需要在开发环境下将 API 请求代理到 API 服务器。这个问题可以通过 vue.config.js 中的 devServer.proxy 配置。

`vue.config.js`

```
module.exports = {
    devServer:{
        proxy:{
            '/api':{
                target: 'http://m.maoyan.com', // 设置你调用的接口域名和端口号
                changeOrigin: true, // 跨域
            },
            '/user':{
                target:'http://192.168.0.42',
                changeOrigin: true
            }
        }
    }
}
```

`... .vue`

```
<script>
export default {
    data() {
        return {};
    },
    methods: {
        getInfo() {
            this.$http({
                method: "get",
                path: "/api/getUserInfo",
            })
                .then((res) => {})
                .catch((err) => {});
        },
    },
};
</script>
```
