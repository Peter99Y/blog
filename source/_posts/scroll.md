---
title: jsx语法
---

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
