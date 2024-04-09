---
title: jsx语法 & vue jsx & tsx
---

### jsx 基础语法

jsx 是**js 语法扩展**，浏览器本身不能识别，需要通过解析工具 babel 才能运行;

```
    <!--引入react核心库-->
    <script crossorigin src="https://unpkg.com/react@17/umd/react.development.js"></script>

    <!--引入react扩展库，用于支持react操作dom-->
    <script crossorigin src="https://unpkg.com/react-dom@17/umd/react-dom.development.js">
    </script>

    <!--引入babel，用于将jsx转成js-->
    <script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>
```

-   jsx 中的 js 表达式要包含在大括号内(if、switch、变量声明是语句不是表达式);
-   推荐 jsx 代码包含在小括号内，防止分号自动插入;
-   jsx 属性值字符串使用引号，变量使用大括号;

```
<body>

    <div id="app"></div>

    <script type="text/babel">
        function getName(val){
            return 'jack ' + val;
        }

        const name = 'ben';

        const isLogin = true;

        const elem = (
            <div>
                <h4>{ name }</h4>

                <h4>{ getName(name) }</h4>
                <h4>{ new Date().getTime() }</h4>

                {isLogin && <h4>登陆中</h4>}
                {isLogin ? <h4>登陆中</h4> : <h4>请登陆</h4>}
            </div>
        );

        ReactDOM.render(elem, document.querySelector('#app'));
    </script>

</body>
```

```
    <script type="text/babel">
        const type = 0;

        function getTypeContent(){
            if(type === 0){
                return <h3>这是0</h3>
            }else if(type === 1){
                return <h3>这是1</h3>
            }else {
                return <h3>这是2</h3>
            }
        }

        const elem = (
            <div>
                {getTypeContent()}
            </div>
        );

        ReactDOM.render(elem, document.querySelector('#app'));
    </script>
```

-   style 控制行内样式
-   通过 className 设置类名控制样式

```
    <script type="text/babel">
        const styleObj = {
            color: 'red',
            fontSize: '20px'
        }

        const elem = (
            <div>
                <h4 style={{color: 'red', fontSize: '20px'}}> hello </h4>

                <h4 style={styleObj}> hello </h4>

                <h4 className="foo"> world </h4>

                <h4 className={`foo ${true && 'item'}`}> world </h4>
            </div>
        );

        ReactDOM.render(elem, document.querySelector('#app'));
    </script>
```

-   map 方法进行遍历，并设置 key

```
    <script type="text/babel">
        const list = [
            {id: 1, name: '你好1'},
            {id: 2, name: '你好2'},
            {id: 3, name: '你好3'},
        ]

        const elem = (
            <ul>
                { list.map(it => <li key={it.id}>{it.name}</li>) }
            </ul>
        );

        ReactDOM.render(elem, document.querySelector('#app'));
    </script>
```

-   使用 on 绑定事件;

```
    <script type="text/babel">
        function handleClick(e) {
            console.log(e)
        }

        function handleClick2(val){
            console.log(val)
        }

        const elem = (
            <div>
                <button onClick={handleClick}> click </button>

                <button onClick={(e) => handleClick2('hello', e)}> click </button>
            </div>
        );

        ReactDOM.render(elem, document.querySelector('#app'));
    </script>
```

### vue render 函数创建 html 模版

> 区别：
> html -> render 函数 -> VNode -> 真是 Dom 节点 (使用&lt;template&gt;模版编译器渲染过程)
> (无 html) render 函数 -> VNode -> 真实 Dom 节点 (render 函数渲染过程)

`son.vue`

```
<script>
export default {
    props: {
        tag: {
            type: String,
            default: "h6",
        },
        list: {
            type: Array,
            default: ()=>{
                return ['hello', 'world']
            }
        }
    },
    render(createElement) {
        return createElement(           // 最终return一个VNode虚拟节点
            this.tag,                   // 1参: 渲染的标签 {String|Object|Fn}

            {                           // 2参: html标签的各种属性
                class: "div-wrapper",   //      设置class
                style: {                //      设置style
                    color: "yellow",
                    fontSize: "40px"
                },
                attrs:{                 //      设置属性
                    "data-index": 1
                },
                on: {
                    click: () => {
                        console.log("click");
                    },
                },
                domProps:{              //      标签内容; (若3参有值会被覆盖)
                    // innerHTML: 'hello'
                }
            },

            // 'hello'                  // 3参: 当前标签的子元素 {String|Array}
            this.list.map((name) => createElement("h5", `${name}`));
        );
    },
};
</script>
```

-   render 可以直接渲染组件

`father.vue`

```
<script>
import son from "./son.vue";
export default {
    render(createElement) {
        return createElement(son, {
            props: {
                msg: "hello",
            },
        });
    },
};
</script>
```

`son.vue`

```
<template>
    <h1>{{msg}}</h1>
</template>
<script>
export default {
    props: {
        msg:String
    },
};
</script>
```

-   render 简化 vue

`father.vue`

```
<template>
    <div class="permit-wrapper">

        <demo tag="h1">hello</demo>

    </div>
</template>

<script>
import demo from './demo.js'
export default {
    components:{
        demo
    }

}
</script>
```

`son.js render渲染模式`

```
export default {
    props: {
        tag:String
    },
    render(createElement){
        // return createElement(this.tag, this.$slots.default);

        const tag = this.tag;
        return (
            <tag>{this.$slots.default}</tag>
        )
    },
};
```

`son.vue 普通模版渲染模式`

```
<template>
    <div>
        <h1 v-if="tag == 'h1'">
            <slot/>
        </h1>
        <h2 v-if="tag == 'h2'">
            <slot/>
        </h2>
        <h3 v-if="tag == 'h3'">
            <slot/>
        </h3>
        ...
    </div>
</template>

<script>
export default {
    props: {
        tag:String
    },
};
</script>
```

### tsx

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
