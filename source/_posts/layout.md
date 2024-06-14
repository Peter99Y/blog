---
title: Layout
---

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
    /* 自适应内容高度 || 最低高度  */
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

### 布局1

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
    overflow: hidden;
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

### 布局2

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
