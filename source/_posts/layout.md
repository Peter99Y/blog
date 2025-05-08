---
title: Layout
---

## 垂直居中

- padding
  缺点：父元素高度是通过子元素决定，需要计算高度；

```css
.container {
  border: 1px solid orange;
  width: 300px;
  text-align: center;
  padding-top: 50px;
  padding-bottom: 50px;
}

.container span {
  font-size: 18px;
}
```

- line-height

缺点：可以允许父元素设置高度，但多行文字会溢出父元素；

```css
.container {
  border: 1px solid orange;
  width: 300px;
  height: 100px;
  text-align: center;
}

.container span {
  line-height: 100px;
}
```

- flex

缺点：IE9 版本以上兼容；

```css
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 300px;
  border: 1px solid orange;
}

.container div.box {
  width: 100px;
  height: 100px;
  background-color: orange;
}
```

```css
.container {
  border: 1px solid orange;
  width: 300px;
  height: 100px;
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.container span {
  line-height: 100px;
}
```

- grid

```css
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

- position

```css
.container {
  border: 1px solid orange;
  position: relative;
  width: 300px;
  height: 300px;
}

.container div.box {
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

###### 左右定宽布局

```css
.clear-fix::after {
  content: "";
  display: block;
  clear: both;
}

.container {
  width: 500%;
  border: 1px solid #000;
}
.aside {
  float: left;
  width: 200px;
  height: 200px;
  background-color: yellowgreen;
}
.content {
  float: right;
  width: 280px;
  height: 200px;
  background-color: orange;
}
```

```html
<div class="container clear-fix">
  <aside class="aside">aside</aside>
  <div class="content">content</div>
</div>
```

###### 左定宽，右侧自适应宽

```css
.container {
  width: 80%;
  border: 1px solid #000;
}
.aside {
  float: left;
  width: 200px;
  height: 200px;
  margin-right: 20px;
  background-color: yellowgreen;
}
.content {
  /* 不能在这里设置margin-left，与左侧浮动元素是重叠是空的*/
  overflow: hidden;
  height: 200px;
  background-color: orange;
}
```

```html
<div class="container">
  <aside class="aside">aside</aside>
  <div class="content">content</div>
</div>
```

###### 左右定宽，中间自适应宽

```css
.container {
  border: 1px solid #000;
}
.clear-fix::after {
  content: "";
  display: block;
  clear: both;
}

.left {
  float: left;
  width: 200px;
  height: 50px;
  margin-right: 20px;
  background-color: yellowgreen;
}
.right {
  float: right;
  width: 200px;
  height: 180px;
  margin-left: 20px;
  background-color: yellowgreen;
}
.content {
  overflow: hidden;
  /* 注意：永远不要加 width !!!  */
  height: 300px;
  background-color: orange;
}
```

```html
<div class="container clear-fix">
  <aside class="left">left</aside>
  <aside class="right">right</aside>
  <div class="content">content</div>
</div>
```

## 自适应高

右侧高度自适应，左侧高度与右侧保持一致

###### flex

```css
.wrapper {
  display: flex;
  align-items: stretch;
  border: 1px solid #000;
}

.wrapper .left {
  min-width: 100px;
  max-width: 100px;
  background-color: yellowgreen;
}

.wrapper .right {
  flex: 1;
  height: 300px;
  background-color: orange;
}
```

###### position

```css
.wrapper {
  position: relative;
}

.wrapper .left {
  width: 100px;
  height: 100%;

  background-color: yellowgreen;
  position: absolute;
}

.wrapper .right {
  height: 300px;
  margin-left: 100px;
  background-color: orange;
}
```

###### margin 负值伪等高

```css
.wrapper {
  overflow: hidden;
}
.wrapper::after {
  content: "";
  display: block;
  clear: both;
}

.wrapper .left {
  float: left;
  background-color: yellowgreen;

  width: 100px;
  margin-right: 10px;
  /*
    盒子总高度 内容 + padding + border + margin = 10px;

    缺点：1.右侧主区域大于了10000像素就不能实现等高；
          2.右侧主区域内容高度比较小，左侧内容高度比右侧内容高度多，左侧会展示不全；
 */
  height: 10000px;
  margin-bottom: -9990px;
}

.wrapper .right {
  height: 300px;
  overflow: hidden;
  background-color: orange;
}
```

## 后台系统布局

### overflow 方式

```css
.container {
  position: fixed;
  width: 100%;
  height: 100%;
}
.header {
  width: 100%;
  height: 60px;
  background-color: #ccc;

  position: absolute;
  left: 0;
  top: 0;
}
.content {
  width: 100%;
  height: 100%;
  /* 不能用 margin-top，子元素如果是定位元素，会多header的 height: 60px 这部分高度；*/
  padding-top: 60px;
}

.content .aside {
  float: left;
  width: 200px;
  height: 100%;
  padding: 20px;
  overflow: auto;
  box-sizing: border-box;
  background-color: yellowgreen;
}
.content .main {
  /* 永远不要加 width !!! */
  height: 100%;
  padding: 20px;
  overflow: auto;
  box-sizing: border-box;
  background-color: orange;
}
```

```html
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

```css
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

```html
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

```css
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

```html
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

## grid

> 当设置 display: grid; 相当于元素被设置成了块元素；inline-grid

```html
<style>
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }
  .grid {
    display: grid;
    width: 500px;
    height: 500px;
    border: 3px solid orange;

    /*
        每列的列宽50px；
        每行的行高50像素；
    */
    grid-template-columns: 100px 100px 100px 100px;
    grid-template-rows: 50px 50px 50px;
  }
</style>

<body>
  <div class="grid">
    <div>1</div>
    <div>2</div>
    <div>3</div>
    <div>4</div>
    <div>5</div>
    <div>6</div>
    <div>7</div>
    <div>8</div>
    <div>9</div>
  </div>
</body>
```

![](/images/css/layout/grid1.png)

#### %

```
/*
    4列的列宽都是100px；
    第1行高20%，第2行高30%，第3行高50%；
*/

grid-template-columns: 100px 100px 100px 100px;
grid-template-rows: 20% 30% 50%;

等价于

grid-template-rows: 2fr 3fr 5fr;
```

![](/images/css/layout/grid2.png)

#### repeat()

```
/*
    重复5列，每列的列宽20%；
    重复3行，每行的行高50px；
*/
grid-template-columns: repeat(5, 20%);
grid-template-rows: repeat(3, 50px);
```

![](/images/css/layout/grid3.png)

#### repeat(auto-fill)

```
/*
    auto-fill会将列宽按照30%划分，不够划分时会留白
*/
grid-template-columns: repeat(auto-fill, 30%);
grid-template-rows: repeat(3, 100px);
```

![](/images/css/layout/grid4.png)

#### auto

```
/*
    第一列宽自适应铺满；
    第二行高自适应铺满；
*/
grid-template-columns: auto 100px 100px;
grid-template-rows: 100px auto 100px;
```

![](/images/css/layout/grid5.png)

#### fr

```
/*
    列宽比例 1:2:3；
    行高比例 3:2:1；
*/
grid-template-columns: 1fr 2fr 3fr;
grid-template-rows: 3fr 2fr 1fr;
```

![](/images/css/layout/grid6.png)

#### minmax(minVal, maxVal)

若子元素像素太长会撑出父容器；

```
/*
    最大值 <= 剩余空间，取最大值；
    最小值 < 剩余空间 < 最大值，填满剩余空间；
    剩余空间 <= 最小值，取最小值；
*/
grid-template-columns: 200px 190px minmax(100px, 200px);
grid-template-rows: 100px 100px 100px;
```

![](/images/css/layout/grid7.png)

#### gap

- gap 是多出来的部分会撑出父容器，即使元素设置了 box-sizing: border-box 也会溢出;
- 设置fr，它们是按比例来的，gap多出来的部分不会撑出父容器；

```
/* 每行间隔20像素，每列间隔10像素 */
row-gap: 20px;
column-gap: 10px;
等价于
gap: 20px 10px;
```

![](/images/css/layout/grid8.png)

#### 排序

```
/* 默认为row水平排序 */
grid-auto-flow: column;
```

![](/images/css/layout/grid9.png)

#### items 项目对齐方式

```
.grid {
    display: grid;
    width: 500px;
    height: 500px;
    border: 3px solid orange;
    margin: 100px auto;

    grid-template-columns: repeat(3, 1fr);
    grid-template-rows: repeat(3, 1fr);

    /*
        单元格内的项目对其方式：
        水平位置；
        垂直位置；
    */
    justify-items: center;
    align-items: center;

    /*
        水平位置 垂直位置；
    */
    place-items: center center;
}

.grid > div{
    width: 100px;
    height: 100px;
    background-color: yellowgreen;
}
```

![](/images/css/layout/grid10.png)

#### content 网格对齐位置

```
    /* 整个网格在父容器位置 */
    justify-content: space-between;
    align-content: center;

    等价于

    place-content: center center;
```

![](/images/css/layout/grid11.png)

#### 合并

- 合并起始位置默认从 1 开始；

- 合并了多少个单元格，需要删除多少个单元格，否则会造成看起来有空隙；

- 指定合并的单元格在 html 排序位置无关；

```html
<style>
  .grid {
    display: grid;
    width: 500px;
    height: 500px;
    border: 3px solid orange;
    margin: 100px auto;

    grid-template-columns: repeat(3, 1fr);
    grid-template-rows: repeat(3, 1fr);

    gap: 10px;
  }

  .grid > div {
    background-color: yellowgreen;
  }

  .row-1 {
    grid-column-start: 1;
    grid-column-end: 4;
  }

  .row-2 {
    grid-column-start: 1;
    grid-column-end: 3;
  }

  .row-3 {
    grid-column-start: 1;
    grid-column-end: 2;
  }
</style>

<body>
  <div class="grid">
    <div class="row-1">1</div>
    <div class="row-2">2</div>
    <div class="row-3">3</div>
  </div>
</body>
```

![](/images/css/layout/grid12.png)

```html
<style>
  .grid {
    display: grid;
    width: 500px;
    height: 500px;
    border: 3px solid orange;
    margin: 100px auto;

    grid-template-columns: repeat(3, 1fr);
    grid-template-rows: repeat(3, 1fr);

    gap: 10px;
  }

  .row-1 {
    /* 列合并的开始/结束位置 */
    /* 
        grid-column-start: 2;
        grid-column-end: 4; 
    */
    grid-column: 2/4;

    /* 行合并的开始/结束位置 */
    /*
        grid-row-start: 1;
        grid-row-end: 3;
    */
    grid-row: 1/3;
  }

  .row-3 {
    grid-column: 1/3;
    grid-row: 3/4;
  }

  .grid > div {
    background-color: yellowgreen;
  }
</style>

<body>
  <div class="grid">
    <div>1</div>
    <div class="row-1">row-1</div>

    <!-- 单元格的html位置可随意 -->
    <div>2</div>

    <div class="row-3">row-3</div>
    <div>3</div>
  </div>
</body>
```

![](/images/css/layout/grid13.png)
