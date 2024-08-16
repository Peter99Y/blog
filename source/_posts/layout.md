---
title: Layout
---

## 垂直居中

-   padding
    缺点：父元素高度是通过子元素决定，需要计算高度；

```
.container{
    border: 1px solid orange;
    width: 300px;
    text-align: center;
    padding-top: 50px;
    padding-bottom: 50px;
}

.container span{
    font-size: 18px;
}
```

-   line-height

缺点：可以允许父元素设置高度，但多行文字会溢出父元素；

```
.container{
    border: 1px solid orange;
    width: 300px;
    height: 100px;
    text-align: center;
}

.container span{
    line-height: 100px;
}
```

-   flex

缺点：IE9 版本以上兼容；

```
.container{
    display: flex;
    justify-content: center;
    align-items: center;
    height: 300px;
    border: 1px solid orange;
}

.container div.box{
    width: 100px;
    height: 100px;
    background-color: orange;
}
```

```
.container{
    border: 1px solid orange;
    width: 300px;
    height: 100px;
    display: flex;
    flex-direction: column;
    justify-content: center;
}

.container span{
    line-height: 100px;
}
```

-   grid

```
.container {
    border: 1px solid orange;
    width: 300px;
    height: 100px;

    display: grid;
    /* 这里是根据3个li，等份排列 */
    grid-template-columns: repeat(3, 1fr);
    align-items: center;
    justify-content: center;
}
```

-   position

```
.container{
    border: 1px solid orange;
    position: relative;
    width: 300px;
    height: 300px;
}

.container div.box{
    width: 100px;
    height: 100px;
    background-color: orange;

    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
}
```

## 自适应宽

###### 左，右定宽布局

```
.clear-fix::after{
    content: '';
    display: block;
    clear: both;
}

.container{
    width: 500%;
    border: 1px solid #000;
}
.aside{
    float: left;
    width: 200px;
    height: 200px;
    background-color: yellowgreen;
}
.content{
    float: right;
    width: 280px;
    height: 200px;
    background-color: orange;
}
```

```
    <div class="container clear-fix">
        <aside class="aside">aside</aside>
        <div class="content"> content </div>
    </div>
```

###### 左定宽，右侧自适应宽

```
.container{
    width: 80%;
    border: 1px solid #000;
}
.aside{
    float: left;
    width: 200px;
    height: 200px;
    margin-right: 20px;
    background-color: yellowgreen;
}
.content{
    /* 不能在这里设置margin-left，与左侧浮动元素是重叠是空的*/
    overflow: hidden;
    height: 200px;
    background-color: orange;
}
```

```
    <div class="container">
        <aside class="aside">aside</aside>
        <div class="content"> content </div>
    </div>
```

###### 左、右定宽，中间自适应宽

```
.container{
    border: 1px solid #000;
}
.clear-fix::after{
    content: '';
    display: block;
    clear: both;
}

.left{
    float: left;
    width: 200px;
    height: 50px;
    margin-right: 20px;
    background-color: yellowgreen;
}
.right{
    float: right;
    width: 200px;
    height: 180px;
    margin-left: 20px;
    background-color: yellowgreen;
}
.content{
    overflow: hidden;
    /* 注意：永远不要加 width !!!  */
    height: 300px;
    background-color: orange;
}
```

```
    <div class="container clear-fix">
        <aside class="left">left</aside>
        <aside class="right">right</aside>
        <div class="content">content</div>
    </div>
```

## 自适应高

右侧高度自适应，左侧高度与右侧保持一致

###### flex

```
.wrapper{
    display: flex;
    align-items: stretch;
    border: 1px solid #000;
}

.wrapper .left{
    min-width: 100px;
    max-width: 100px;
    background-color: yellowgreen;
}

.wrapper .right{
    flex: 1;
    height: 300px;
    background-color: orange;
}
```

###### position

```
.wrapper{
    position: relative;
}

.wrapper .left{
    width: 100px;
    height: 100%;

    background-color: yellowgreen;
    position: absolute;
}

.wrapper .right{
    height: 300px;
    margin-left: 100px;
    background-color: orange;
}
```

###### margin 负值伪等高

```
.wrapper{
    overflow: hidden;
}
.wrapper::after{
    content: '';
    display: block;
    clear: both;
}

.wrapper .left{
    float: left;
    background-color: yellowgreen;

    width: 100px;
    margin-right: 10px;
    /*
        盒子总高度 内容+padding+border+margin = 10px;

        缺点：1.右侧主区域大于了10000像素就不能实现等高；
             2.右侧主区域内容高度比较小，左侧内容高度比右侧内容高度多，左侧会展示不全
    */
    height: 10000px;
    margin-bottom: -9990px;
}

.wrapper .right{
    height: 300px;
    overflow: hidden;
    background-color: orange;
}
```

## 后台系统布局

### overflow 方式

```
.container{
    position: fixed;
    width: 100%;
    height: 100%;
}
.header{
    width: 100%;
    height: 60px;
    background-color: #ccc;

    position: absolute;
    left: 0;
    top: 0;
}
.content{
    width: 100%;
    height: 100%;
    margin-top: 60px;
}

.content .aside{
    float: left;
    width: 200px;
    height: 100%;
    padding: 20px;
    overflow: auto;
    box-sizing: border-box;
    background-color: yellowgreen;
}
.content .main{
    // 永远不要加 width !!!
    height: 100%;
    padding: 20px;
    overflow: auto;
    box-sizing: border-box;
    background-color: orange;
}
```

```
    <div class="container">
        <header class="header">logo</header>
        <div class="content">
            <aside class="aside">aside</aside>
            <div class="main">main</div>
        </div>
    </div>
```

![](/images/layout/layout.png)

### flex 方式

```
* {
    margin: 0;
    padding: 0;
}

.container {
    display: flex;
    height: 100vh;
    overflow: hidden;
}

.slide {
    min-width: 200px;
    border-right: 1px solid #ccc;
    height: 100%;
}

.main {
    display: flex;
    flex-direction: column;
    flex: 1;
}

.main .header {
    height: 60px;
    border-bottom: 1px solid #ccc;
}

.main .content {
    flex: 1;
    overflow: auto;
}
```

```
<div class="container">
    <div class="slide">slide</div>

    <div class="main">
        <div class="header">header</div>
        <div class="content">content</div>
    </div>
</div>
```

![](/images/layout/layout2.png)

### fixed 方式

```
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

.slide {
    width: 200px;
    height: 100vh;
    background: orange;
}

.header {
    position: fixed;
    top: 0;
    left: 200px;
    width: calc(100% - 200px);
    height: 90px;
    background-color: lightblue;
}

.main {
    position: fixed;
    left: 200px;
    top: 90px;
    width: calc(100% - 200px);
    height: calc(100vh - 90px);
    overflow: auto;
    background: yellowgreen;
}
```

```
<div class="container">
    <div class="slide">slider</div>
    <div class="header">header</div>
    <div class="main">
        <p style="height: 200px;border: 1px solid #ccc;width: 100%;">1</p>
        <p style="height: 200px;border: 1px solid #ccc;width: 100%;">1</p>
        <p style="height: 200px;border: 1px solid #ccc;width: 100%;">1</p>
        <p style="height: 200px;border: 1px solid #ccc;width: 100%;">1</p>
        <p style="height: 200px;border: 1px solid #ccc;width: 100%;">1</p>
    </div>
</div>
```

![](/images/layout/layout3.png)
