---
title: Css
---

## css 属性值计算过程

- 1.确定声明值 (将两样式表中**没有冲突的 css 属性**，作为最终的 css 属性值)

  - 浏览器默认样式表 (user agent stylesheet)
  - 用户自定义样式表 (element.style)

- 2.层叠冲突 (对两种样式表中冲突的 css 属性使用层叠规则，确定 css 属性值)

  - 比较重要性（作者样式表权重高于浏览器样式表并覆盖相同属性的值）
  - 比较 css 选择器权重
  - 比较 css 选择器内的相同属性次序

- 3.使用继承
  对仍然没有值的 css 属性，继承父元素的值 （文字相关属性 color,fontSize,fontFamily,textAlign 等; 不能继承 display）
- 4.使用默认值
  对仍然没有值的 css 属性，使用默认值（backgroundColor:transparent 等）

```
<style> .container{color: red } </style>

<div class="container">
    <a> click </a>
    <h1> p </h1>
</div>

h1元素
1. 自定义样式表没有对h1元素设置color;
   浏览器默认样式表没设置color，但有 h1 {
        font-weight: bold,
        font-size: 2em;
        margin-block-start: 0.67em;
        margin-block-end: 0.67em;
    };

2. h1的样式表中没有color属性冲突;
3. 继承父元素的color红色;

a元素
1. 自定义样式表没有对a元素直接设置color;
   浏览器默认样式表没设置color，但有 a:-webkit-any-link {
        color: -webkit-link; // 默认颜色
        cursor: pointer;
        text-decoration: underline;
   }

由于第1步骤 浏览器默认样式表 中就确定了color属性，也就不会走第2,3,4步骤了;
设置用户自定义样式表color:inherit or 其他颜色; color就会在第2步骤 **比较重要性** 中覆盖颜色;
```

## width & height

- width 能被子元素继承，height 不能被子元素继承；
- min/max-height 只对当前元素生效，不能被子元素继承，它只是约束条件；
  ...

## 权重

| 属性                | 大小 |
| ------------------- | ---- |
| !important          |      |
| style               | 1000 |
| id                  | 100  |
| class、伪类、[属性] | 10   |
| 标签、伪元素        | 1    |
| \*                  | 0    |

## 包含块

- 元素的排列是在一块区域中，这个区域包含了这个元素，所以元素的包含块就是元素的排列区域；

- 普通元素 和 浮动元素 的包含块就是父元素的 width 内容区域 (不包含父元素 padding、border、margin 宽度)；
  元素的 width/height 的百分比，是相对于父元素的内容区域；

- 绝对定位元素的包含块是带有定位父级元素的 padding 区域 (padding + width)；
  绝对定位元素的 left/top 的值，width/height 的百分比，是相对于父级绝对定位元素的 padding 区域；

## bfc

Block Formatting Context 块级格式化上下文，它是一块**独立的渲染区域**，它规定了在该区域中，常规流**块元素**的布局;

##### 独立的渲染区域

块盒的排列布局是在一个区域里面，这个区域就是块级格式化上下文，就是 bfc 创建的区域；
不同的 bfc 区域互不干扰，这个区域隔绝了内部和外部的联系，内部的渲染不会影响到外部，不会跟其他元素重叠或覆盖；

以下元素会创建内部的 bfc;

- 根元素 html;
- 浮动元素 float;
- 绝对定位元素 position: fixed/absolute;
- overflow 非 visible 的块盒;

##### 常规流块元素布局

- 在水平方向上撑满包含块，垂直方向上依次摆放;
- 父子关系下，则垂直方向上外边距 margin 塌陷/margin 合并;
- 父子关系下，父元素会无视浮动的子元素产生高度塌陷;
- 兄弟关系下，左侧浮动元素会覆盖右侧常规流元素;

##### bfc 解决方案

- bfc 元素和它的子元素不会外边距合并 (父子关系);
  - 将元素设置成绝对定位或浮动(不推荐：影响元素在页面的布局);
  - 父元素设置 border 或 padding(不推荐：影响元素);
  - 父元素设置 display: inline-block (不推荐：影响元素在页面的布局);
  - 父元素设置 overflow;

```
<style>
    *{
        margin: 0;
        padding: 0;
    }
    .father{
        background-color: yellowgreen;
        height: 200px;
        margin-top: 20px;
        overflow: hidden;
    }
    .child{
        background-color: orange;
        height: 50px;
        margin-top: 50px;
    }
</style>

<body>
    <div class="father">
        <div class="child">margin-top:50</div>
    </div>
</body>
```

![](/images/Css/margin-top.jpg)

- bfc 元素的高度计算了子浮动元素 (父子关系);
  - 父元素::after{clear: both; content: '', display: block};
  - 将元素设置成绝对定位或浮动(不推荐：影响元素在页面的布局);
  - 父元素设置 display: inline-block (不推荐：影响元素在页面的布局);
  - 父元素设置 overflow;

```
<style>
    *{
        margin: 0;
        padding: 0;
    }
    .father{
        border: 1px solid black;
        overflow: hidden;
    }
    .son{
        width: 100px;
        height: 100px;
        background-color: orange;
        margin-right: 20px;
        float: left;
    }
</style>

<div class="father">
    <div class="son"></div>
    <div class="son"></div>
    <div class="son"></div>
</div>
```

- 浮动元素 与 bfc 元素不会重叠 (兄弟关系);
  - 将元素设置成绝对定位或浮动(不推荐：影响元素在页面的布局);
  - 父元素设置 display: inline-block (不推荐：影响元素在页面的布局);
  - 将常规流元素设置成 bfc 元素 overflow: 非 visible 即可(hidden/auto/scroll)

```
<style>
    *{
        margin: 0;
        padding: 0;
    }
    .float{
        width: 100px;
        height: 100px;
        background-color: tomato;
        float: left;
        margin-right: 10px;
    }
    .right{
        height: 200px;
        background-color: yellowgreen;
        overflow: hidden;
    }
</style>

<body>
    <div class="float"></div>
    <div class="right"></div>
</body>
```

![](/images/Css/overflow.jpg)

## position

所有浮动元素和定位元素都会默认变成块元素；
如果给行元素添加定位，那么就会变成块元素，并且会脱离文档流；

##### absolute

基于有定位的父元素

##### fixed

基于视口 viewport

#### sticky

- 元素设置了粘性布局之后，会受到两个因素的影响： - 1.包含块； - 2.最近可滚动元素，否则就是适口（body）
  ps：可滚动元素（overflow: 非 visible，它的意思就是不会出现滚动；hidden, scroll, auto 都是可滚动元素）；

```
    <style>
        dt {
            font-weight: bold;
            background-color: orange;
            position: sticky;
            top: 0;
        }
        dd {
            border-bottom: 1px solid #ccc;
        }
    </style>
```

```
<body>
    <dl>
        <dt>A</dt>
        <dd>Lorem ipsum dolor sit amet consectetur, adipisicing elit. Id in officia nisi nulla adipisci illo dignissimos
            doloribus delectus provident. Officiis, ratione. Dicta ratione fuga perferendis, provident saepe soluta
            explicabo accusantium.</dd>
    </dl>
    <dl>
        <dt>B</dt>
        <dd>Lorem ipsum dolor sit amet consectetur, adipisicing elit. Id in officia nisi nulla adipisci illo dignissimos
            doloribus delectus provident. Officiis, ratione. Dicta ratione fuga perferendis, provident saepe soluta
            explicabo accusantium.</dd>
    </dl>
</body>
```

![](/images/Css/sticky.png)

---

## 伪元素 ::before/::after

- 伪元素默认是行元素，无法设置宽高；变块元素，需要设置 display: block 或 position: absolute (会将行颜色设置为块元素)

- content 可设置字符串、空白、图片、计数器、attr（通过元素上的属性值设置字符串）;

```html
<style>
  div::after {
    content: attr(label-suffix);
    /* content: "："; */
  }
</style>

<div label-suffix="：">名称</div>
```

![](/images/Css/content_attr.png)

```html
<style>
  .heart {
    width: 100px;
    height: 100px;
    background-color: #e74c3c;
    transform: rotate(45deg);
  }
  .heart::before {
    content: "";
    width: 100px;
    height: 100px;
    border-radius: 50%;
    position: absolute;
    background-color: #e74c3c;
    transform: translateX(-50%);
  }
  .heart::after {
    content: "";
    width: 100px;
    height: 100px;
    border-radius: 50%;
    position: absolute;
    background-color: #e74c3c;
    transform: translateY(-50%);
  }
</style>
```

![](/images/Css/heart.png)

---

## 伪类

| 伪类              | 含义                                                         |
| ----------------- | ------------------------------------------------------------ |
| :link             | 选中未访问过的**超链接**                                     |
| :visited          | 选中已访问过的**超链接**                                     |
| :hover            | 选中鼠标悬停的元素                                           |
| :acitve           | 选中鼠标按下的元素                                           |
| \*                | \*                                                           |
| :focus            | 选中获得焦点的**表单元素**                                   |
| :disabled         | 选中被禁用的**表单元素**                                     |
| :checked          | 选中被选中的**表单元素**                                     |
| \*                | \*                                                           |
| nth-child(an+b)   | 选中第 an+b 个子元素，a 和 b 是常亮，n 从 0 开始递增         |
| first-child       | 选中第一个子元素                                             |
| last-child        | 选中最后一个子元素                                           |
| first-of-type     | 选中第一个指定类型的子元素                                   |
| last-of-type      | 选中最后一个指定类型的子元素                                 |
| nth-of-type(an+b) | 选中第 an+b 个指定类型子元素，a 和 b 是常亮，n 从 0 开始递增 |

###### :has

通过判断子元素条件设置父元素样式;

```
    // 如果div下有h5元素，就给div设置背景颜色
    div:has(h5){
        background-color: orange;
    }
```

```
    <div>
        <h4>1</h4>
    </div>

    <div>
        <h5>2</h5>
    </div>
```

![](/images/Css/has.png)

###### :is / :where

```
    <style>
        /*
            .line1 h4,
            .line2 h4,
            .line3 h4 {
                color: orange;
            }
        */

        等价于

        :is(.line1, .line2, .line3) h4 {
            color: orange;
        }

        等价于

        :where(.line1, .line2, .line3) h4{
            color: orange;
        }
    </style>
```

```
    <div class="line1">
        <h4>hello</h4>
    </div>

    <div class="line2">
        <h4>hello</h4>
    </div>

    <div class="line2">
        <h4>hello</h4>
    </div>
```

###### :not

- 注意：p:not(.world){...} 如 p :not 中间空格，否则认为是下级元素；

```
    p:not(.world){
        color: orange;
    }
```

```
   <p>hello</p>
   <p class="world">world</p>
```

![](/images/Css/not1.png)

---

- 在 p 元素下给 span 元素设置 color

```
    p span:not(.world){
        color: orange;
    }
```

```
    <p>
        <span>hello</span>
    </p>

    <p>
        <span class="world">world</span>
    </p>
```

![](/images/Css/not2.png)

---

- div 元素下给既不是 h3 也不是 h5 元素设置 color

```
    div :not(h3):not(h5){
        color: orange;
    }
```

```
    <div>
        <h3>1</h3>
        <h4>2</h4>
        <h5>3</h5>
    </div>
```

---

## display

移除 DOM 元素（导致会重绘、会重排）

- 1. 事件不会触发；

- 2. 脱离文档流；

- 3. 子元素受影响；

- 4. 过渡动画无效；

```
        .select-wrapper {
            cursor: pointer;
            position: relative;
            margin-bottom: 50px;
        }

        .select-wrapper:hover .select-content {
            display: block;
        }

        .select-content {
            margin-top: 12px;
            position: absolute;
            left: 0;

            /* 4. 过渡动画无效 */
            display: none;
            transition: all 0.2s ease;
        }
```

```
    <div class="select-wrapper">
        <span> 消息⬇ </span></span>

        <div class="select-content">
            <div style="display: none;">
                <a href="#" style="display: block;">关注</a>
            </div>
            <a href="#">点赞</a>
            <a href="#">收藏</a>
        </div>
    </div>
```

![](/images/Css/display.png)

## visibility

会重绘

- 1. visibility: hidden 时，元素不会触发事件；
- 2. 不脱离文档流（即不会重排），后面元素不会顶上来；
- 3. 子元素可设置 visible，从而不受父元素 hidden 的影响；
- 4. 有过渡动画效果；

```
    .select-wrapper {
        cursor: pointer;
        position: relative;
        margin-bottom: 50px;
    }

    .select-wrapper:hover .select-content {
        visibility: visible;
    }

    .select-content {
        margin-top: 12px;
        position: absolute;
        left: 0;

        transition: all 0.2s ease;
        visibility: hidden;
    }
```

```
    <div class="select-wrapper">
        <span> 消息⬇ </span></span>

        <div class="select-content">
            <div style="visibility: hidden;">
                <a href="#" style="visibility: visible;">关注</a>
            </div>
            <div><a href="#">点赞</a></div>
            <div><a href="#">收藏</a></div>
        </div>
    </div>
```

## opacity

只是改变元素透明度（不重排、不重绘），改变元素内所有子元素(文字、背景色等)透明度；
使用 rgba 设置更精细，只改变单个属性（文字，背景等）的透明度；

- 1. opacity:0 时，点击元素会触发事件；
- 2. 不会脱离文档流，也不会重绘，只是降低了 alpha 值；
- 3. 子元素自身设置 opacity:1，也会被父元素的 opacity 透明度影响；
- 4. 有过渡动画效果；

```
    <div style="
            width: 100px;
            height: 200px;
            background-color: orange;
            opacity: 0;">
        <span style="opacity: 1">son</span>
    </div>

    <script>

        let i = document.querySelector('div')
        i.addEventListener('click', function () {
            console.log('i')
        })
    </script>
```

## linear-gradient

linear adj.线性的; gradient n.斜坡; adj.倾斜的;

background: 可多个 linear-gradient 函数，与属性名越近的层级越高，图层越在上面；

```
background-image:
	linear-gradient(to bottom, transparent 0%, #fff 80%),
    linear-gradient(to right, #ba8782 45%, #89CFF0);
```

## 文字阴影

```
    h1 {
        color: #e6e6e6;
        font-size: 100px;

        /* x轴，y轴，模糊半径，颜色，逗号间隔可添加多个阴影 */
        text-shadow:
            1px -1px #fff,
            -1px 1px #999,
            -10px 10px 5px #808080;

        /* 第一个：从右上角度照射高亮边角
        第二个：左下角阴影
        第三个：文字背景阴影（对应照射角度） */
    }
```

![](/images/Css/text-shadow.jpg)

## clip-path

裁剪一个元素成任意类型
circle - 圆形; inset - 矩形; ellipse - 椭圆; polygon - 多边形

```
        img {
            margin: 10px auto;
            width: 100px;
            display: block;
        }

        .img-1 {
            /* 长度 at 圆心处于元素x位置 圆心处于元素y位置 */
            clip-path: circle(50% at center center)
        }

        .img-2 {
            clip-path: circle(100% at 100% 100%)
        }

        .img-3 {
            /* 上右下左 */
            clip-path: inset(0% 32% 0% 46%);
        }

        .img-4 {
            /* 每个点的x & y轴位置 */
            clip-path: polygon(50% 0%, 100% 50%, 50% 100%, 0% 50%);
        }
```

![](/images/Css/clip-path.jpg)

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

![](/images/Css/grid1.png)

---

###### %

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

![](/images/Css/grid2.png)

---

###### repeat()

```
/*
    重复5列，每列的列宽20%；
    重复3行，每行的行高50px；
*/
grid-template-columns: repeat(5, 20%);
grid-template-rows: repeat(3, 50px);
```

![](/images/Css/grid3.png)

---

###### repeat(auto-fill)

```
/*
    auto-fill会将列宽按照30%划分，不够划分时会留白
*/
grid-template-columns: repeat(auto-fill, 30%);
grid-template-rows: repeat(3, 100px);
```

![](/images/Css/grid4.png)

---

###### auto

```
/*
    第一列宽自适应铺满；
    第二行高自适应铺满；
*/
grid-template-columns: auto 100px 100px;
grid-template-rows: 100px auto 100px;
```

![](/images/Css/grid5.png)

###### fr

```
/*
    列宽比例 1:2:3；
    行高比例 3:2:1；
*/
grid-template-columns: 1fr 2fr 3fr;
grid-template-rows: 3fr 2fr 1fr;
```

![](/images/Css/grid6.png)

---

###### minmax(minVal, maxVal)

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

![](/images/Css/grid7.png)

###### gap

gap 是多出来的部分会撑出父容器，即使元素使用了 box-sizing: border-box;

```
/* 如设置repeat(5, 1fr)，1fr是按比例来的，gap多出来的部分不会撑出父容器；*/
grid-template-columns: repeat(5, 20%);
grid-template-rows: repeat(5, 20%);


/* 每行间隔20像素，每列间隔10像素 */
row-gap: 20px;
column-gap: 10px;
等价于
gap: 20px 10px;
```

![](/images/Css/grid8.png)

---

###### 排序

```
/* 默认为row水平排序 */
grid-auto-flow: column;
```

![](/images/Css/grid9.png)

---

###### items 项目对齐方式

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

![](/images/Css/grid10.png)

---

###### content 网格对齐位置

```
    /* 整个网格在父容器位置 */
    justify-content: space-between;
    align-content: center;

    等价于

    place-content: center center;
```

![](/images/Css/grid11.png)

---

###### 合并

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

![](/images/Css/grid12.png)

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

![](/images/Css/grid13.png)

## scrollbar

```
// 滚动条外观
::-webkit-scrollbar{
    width: 10px;
}

::-webkit-scrollbar-track {
    background-color: #ddd;
}

::-webkit-scrollbar-thumb {
    width: 10px;
    background-color: #ccc;
    border-radius: 10px;
}
```
