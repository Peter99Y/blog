---
title: Css
---

## css 通用属性值

initial 将元素的属性重置为默认值 (注意 p、b、ul、h1 等元素有默认 margin 和 padding 值);
unset 将元素的属性重置为继承的值，否则使用默认值;

```html
<style>
  p {
    margin: initial;
  }

  ul {
    all: unset;
  }
</style>

<body>
  <div style="color: blue">
    <p style="color: red"> 展示红色 </p>
  </div>

  <div style="color: blue">
    <p style="color: unset"> 展示蓝色 </p>
  </div>
</body>
```

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

![](/images/css/css/margin-top.jpg)

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

![](/images/css/css/overflow.jpg)

## float

半脱离文档流；
自动变为块元素，自动设置了 display: block 属性；

```html
<style>
  .box {
    width: 100px;
    height: 100px;
  }
  .box1 {
    background-color: orange;
    float: left;
  }
  .box2 {
    background-color: skyblue;
    float: left;
  }
  .box3 {
    background-color: red;
    float: left;
  }
</style>
```

![](/images/css/css/float1.png)

```html
<style>
  .box {
    width: 100px;
    height: 100px;
  }
  .box1 {
    background-color: orange;
  }
  .box2 {
    background-color: skyblue;
    float: left;
  }
  .box3 {
    background-color: red;
    width: 130px;
    height: 130px;
  }
</style>

<body>
  <div class="box box1">box1</div>
  <div class="box box2">float</div>
  <div class="box box3">box3</div>
</body>
```

![](/images/css/css/float2.png)

```html
<style>
  .box {
    background-color: orange;
    float: left;
  }
</style>

<body>
  <div class="box">Lorem ipsum dolor sit</div>
</body>
```

![](/images/css/css/float3.png)

---

## position

所有浮动元素和定位元素都会默认变成块元素；
给行元素添加定位，会变成块元素，并且会脱离文档流；

##### absolute

基于有定位的父元素

##### fixed

基于视口 viewport

#### sticky

- 元素设置了粘性布局之后，会受到两个因素的影响： - 1.包含块； - 2.最近可滚动元素，否则就是适口（body）
  ps：可滚动元素（overflow: 非 visible，它的意思就是不会出现滚动；hidden, scroll, auto 都是可滚动元素）；

```html
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

<body>
  <dl>
    <dt>A</dt>
    <dd>
      Lorem ipsum dolor sit amet consectetur, adipisicing elit. Id in officia
      nisi nulla adipisci illo dignissimos doloribus delectus provident.
      Officiis, ratione. Dicta ratione fuga perferendis, provident saepe soluta
      explicabo accusantium.
    </dd>
  </dl>
  <dl>
    <dt>B</dt>
    <dd>
      Lorem ipsum dolor sit amet consectetur, adipisicing elit. Id in officia
      nisi nulla adipisci illo dignissimos doloribus delectus provident.
      Officiis, ratione. Dicta ratione fuga perferendis, provident saepe soluta
      explicabo accusantium.
    </dd>
  </dl>
</body>
```

![](/images/css/css/sticky.png)

---

## 伪元素 ::before/::after

- 伪元素默认是行元素，无法设置宽高；变块元素需要设置 display: block 或 position: absolute (会将行元素设置为块元素)

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

![](/images/css/css/content_attr.png)

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

![](/images/css/css/heart.png)

---

## 伪类

| 伪类              | 含义                                                         |
| ----------------- | ------------------------------------------------------------ |
| :link             | 选中未访问过的**超链接**                                     |
| :visited          | 选中已访问过的**超链接**                                     |
| :hover            | 选中鼠标悬停的元素                                           |
| :acitve           | 选中鼠标按下的元素                                           |
| -                 | -                                                            |
| :focus            | 选中获得焦点的**表单元素**                                   |
| :disabled         | 选中被禁用的**表单元素**                                     |
| :checked          | 选中被选中的**表单元素**                                     |
| -                 | -                                                            |
| nth-child(an+b)   | 选中第 an+b 个子元素，a 和 b 是常亮，n 从 0 开始递增         |
| first-child       | 选中第一个子元素                                             |
| last-child        | 选中最后一个子元素                                           |
| first-of-type     | 选中第一个指定类型的子元素                                   |
| last-of-type      | 选中最后一个指定类型的子元素                                 |
| nth-of-type(an+b) | 选中第 an+b 个指定类型子元素，a 和 b 是常亮，n 从 0 开始递增 |

#### :has

通过判断条件设置父元素样式;

```html
<style>
  /* 如果div下有h5元素，就给div设置背景颜色 */
  div:has(h5) {
    background-color: orange;
  }
</style>

<body>
  <div>
    <h4>1</h4>
  </div>

  <div>
    <h5>2</h5>
  </div>
</body>
```

![](/images/css/css/has.png)

#### :not

```html
<style>
  p:not(.world) {
    color: orange;
  }
</style>

<body>
  <p>hello</p>
  <p class="world">world</p>
</body>
```

![](/images/css/css/not1.png)

---

- div 元素下, 给既不是 h3 也不是 h5 元素设置 color；
  注意：p:not(.world){...} p 与:not 中间不能有空格，否则被认为是给下级元素添加样式；

```html
<style>
  div :not(h3):not(h5) {
    color: orange;
  }
</style>

<body>
  <div>
    <h3>1</h3>
    <h4>2</h4>
    <h5>3</h5>
  </div>
</body>
```

---

- 给 span 元素设置 color

```html
<style>
  p span:not(.world) {
    color: orange;
  }
</style>

<body>
  <p>
    <span>hello</span>
  </p>

  <p>
    <span class="world">world</span>
  </p>
</body>
```

![](/images/css/css/not2.png)

#### :is / :where

```html
<style>
  /*
      .line1 h4,
      .line2 h4,
      .line3 h4 {
          color: orange;
      }
  */

  /* 等价于  */
  :is(.line1, .line2, .line3) h4 {
    color: orange;
  }

  /* 等价于  */
  :where(.line1, .line2, .line3) h4 {
    color: orange;
  }
</style>

<body>
  <div class="line1">
    <h4>hello</h4>
  </div>

  <div class="line2">
    <h4>hello</h4>
  </div>

  <div class="line2">
    <h4>hello</h4>
  </div>
</body>
```

## display

移除 DOM 元素（导致会重绘、会重排）

- 1. 事件不会触发；

- 2. 脱离文档流；

- 3. 子元素受影响；

- 4. 过渡动画无效；

```html
<style>
  .wrapper:hover .content {
    display: block;
  }

  .content {
    /* hover切换过渡动画无效 */
    display: none;
    transition: all 0.2s ease;
  }
</style>

<body>
  <div class="wrapper">
    <span> hover </span>

    <div class="content">
      <!-- 父级display: none; 子级display: block依然无效 -->
      <a href="#" style="display: block">关注</a>
    </div>
  </div>
</body>
```

![](/images/css/css/display.png)

## visibility

会重绘

- 1. visibility: hidden 时，元素不会触发事件；
- 2. 不脱离文档流（即不会重排），后面元素不会顶上来；
- 3. 子元素可设置 visible，从而不受父元素 hidden 的影响；
- 4. 有过渡动画效果；

```html
<div class="content" style="visibility: hidden">
  <a href="#" style="visibility: visible">关注</a>
</div>
```

## opacity

只是改变元素透明度（不重排、不重绘），改变元素内所有子元素(文字、背景色等)透明度；
使用 rgba 设置更精细，只改变单个属性（文字，背景等）的透明度；

- 1. opacity:0 时，点击元素会触发事件；
- 2. 不会脱离文档流，也不会重绘，只是降低了 alpha 值；
- 3. 子元素自身设置 opacity:1，也会被父元素的 opacity 透明度影响；
- 4. 有过渡动画效果；

```html
<body>
  <div
    style="
      width: 100px;
      height: 200px;
      background-color: orange;
      opacity: 0;
    "
  >
    <span style="opacity: 1">son</span>
  </div>

  <script>
    let i = document.querySelector("div");
    i.addEventListener("click", function () {
      console.log("i");
    });
  </script>
</body>
```

## linear-gradient

linear adj.线性的; gradient n.斜坡; adj.倾斜的;

background: 可多个 linear-gradient 函数，与属性名越近的层级越高，图层越在上面；

```
background-image:
	linear-gradient(to bottom, transparent 0%, #fff 80%),
    linear-gradient(to right, #ba8782 45%, #89CFF0);
```

## clip-path

裁剪一个元素成任意类型
circle - 圆形; inset - 矩形; ellipse - 椭圆; polygon - 多边形

```html
<style>
  img {
    margin: 10px auto;
    width: 100px;
    display: block;
  }

  .img-1 {
    /* 长度 at 圆心处于元素x位置 圆心处于元素y位置 */
    clip-path: circle(50% at center center);
  }

  .img-2 {
    clip-path: circle(100% at 100% 100%);
  }

  .img-3 {
    /* 上右下左 */
    clip-path: inset(0% 32% 0% 46%);
  }

  .img-4 {
    /* 每个点的x & y轴位置 */
    clip-path: polygon(50% 0%, 100% 50%, 50% 100%, 0% 50%);
  }
</style>
```

![](/images/css/css/clip-path.jpg)

## scrollbar

```css
::-webkit-scrollbar {
  /* 垂直滚动条的宽度 */
  width: 10px;
  /* 水平滚动条高度 */
  height: 10px;
}

/* 轨道 */
::-webkit-scrollbar-track {
  background-color: #ddd;
  -webkit-box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.2);
}

/* 滑块 */
::-webkit-scrollbar-thumb {
  width: 10px;
  background-color: #ccc;
  border-radius: 10px;
}
```

## white-space & word-break

用于控制元素内的空白字符（如空格、制表符、换行符等）如何处理，以及文本是否和何时进行换行；
但是影响有限，如果是一串数字，不会强制文本在特定宽度内换行。
如果文本中没有空格或其他允许断行的字符（如标点符号），浏览器可能会将整个单词视为一个不可分割的整体，导致溢出

```html
<body>
  <div
    style="
        width: 100px;
        height: 50px;
        border: 1px solid black;
        white-space: normal;
      "
  >
    1111122233333
  </div>

  <div
    style="
        width: 100px;
        height: 50px;
        border: 1px solid black;
        white-space: normal;
      "
  >
    aaaaabbbbbcccc
  </div>

  <div
    style="
        width: 100px;
        height: 50px;
        border: 1px solid black;
        white-space: normal;
      "
  >
    111 1111113333444
  </div>

  <div
    style="
        width: 100px;
        height: 50px;
        border: 1px solid black;
        white-space: normal;
        word-break: break-all;
      "
  >
    111111111113333
  </div>
</body>
```

![](/images/css/css/white-space.png)
