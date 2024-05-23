---
title: Css
---

## bfc

Block Formatting Context 块级格式化上下文，它是一块**独立的渲染区域**，它规定了在该区域中，常规流**块元素**的布局;

##### 独立的渲染区域

块盒的排列布局是在一个区域里面，这个区域就是块级格式化上下文，就是 bfc 创建的区域；
不同的 bfc 区域互不干扰，这个区域隔绝了内部和外部的联系，内部的渲染不会影响到外部，不会跟其他元素重叠或覆盖；

以下元素会创建内部的 bfc;

-   根元素 html;
-   浮动元素 float;
-   绝对定位元素 position: fixed/absolute;
-   overflow 非 visible 的块盒;

##### 常规流块元素布局

-   在水平方向上撑满包含块，垂直方向上依次摆放;
-   父子关系下，则垂直方向上外边距 margin 塌陷/margin 合并;
-   父子关系下，父元素会无视浮动的子元素产生高度塌陷;
-   兄弟关系下，左侧浮动元素会覆盖右侧常规流元素;

##### bfc 解决方案

-   bfc 元素和它的子元素不会外边距合并 (父子关系);
    -   将元素设置成绝对定位或浮动(不推荐：影响元素在页面的布局);
    -   父元素设置 border 或 padding(不推荐：影响元素);
    -   父元素设置 display: inline-block (不推荐：影响元素在页面的布局);
    -   父元素设置 overflow;

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

-   bfc 元素的高度计算了子浮动元素 (父子关系);
    -   父元素::after{clear: both; content: '', display: block};
    -   将元素设置成绝对定位或浮动(不推荐：影响元素在页面的布局);
    -   父元素设置 display: inline-block (不推荐：影响元素在页面的布局);
    -   父元素设置 overflow;

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

-   浮动元素 与 bfc 元素不会重叠 (兄弟关系);
    -   将元素设置成绝对定位或浮动(不推荐：影响元素在页面的布局);
    -   父元素设置 display: inline-block (不推荐：影响元素在页面的布局);
    -   将常规流元素设置成 bfc 元素 overflow: 非 visible 即可(hidden/auto/scroll)

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

---

## 包含块

-   元素的排列是在一块区域中，这个区域包含了这个元素，所以元素的包含块就是元素的排列区域；

-   浮动元素 or 普通元素的包含块就是父元素的 width 内容区域(不包含父元素 padding、border、margin 宽度)；元素的 width/height 的**百分比**宽度就是相对于父元素的 width/height；

-   绝对定位元素的包含块是带有定位元素 padding+width 区域；绝对定位元素的 left/top 是相对于 padding+width 区域；

## 粘性布局

position: sticky;

-   元素设置了粘性布局之后，会受到两个因素的影响： - 1.包含块； - 2.最近可滚动元素，否则就是适口（body）
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

## 伪元素 ::before/::after

-   content 可设置字符串、空白、图片、计数器、attr（通过元素上的属性值设置字符串）;

```
div::after {
    content: attr(label-suffix);
    /* content: "："; */
}
```

```
<div label-suffix="：">名称</div>
```

![](/images/Css/content_attr.png)

## 伪类

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

###### :is/:where

```
    <style>
        /*
            .line1 h4,
            .line2 h4,
            .line3 h4 {
                color: orange;
            }
        */

        :is(.line1, .line2, .line3) h4 {
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

-   注意：p:not(.world){...} 如 p :not 中间空格，否则认为是下级元素；

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

-   在 p 元素下给 span 元素设置 color

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

-   div 元素下给既不是 h3 也不是 h5 元素设置 color

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

会重绘、会重排

-   1. 不会触发 - select-content 内容在 none 的情况下，虽然元素存在，我们也知道这块区域存这块内容，
       但是无论如何在隐藏的空白区域内点击都不会有反应；

-   2. 脱离文档流 - div 元素在 none 的情况下，后面会内容顶上来；

-   3. 子元素受影响 - div 元素在 none 的情况下，里面的子元素也会跟着隐藏（即使子元素又设置了 block）

-   4. 过渡动画无效；

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

-   1. 不触发事件 - 自身 hidden 情况下，不会触发自身事件；
-   2. 不脱离文档流（即不会重排），后面元素不会顶上来；
-   3. 子元素可设置 visible，从而不受父元素 hidden 的影响；
-   4. 过渡动画效果有效；

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

### opacity

不重排、不重绘

-   1. 会触发事件；
-   2. 不会脱离文档流，也不会重绘，只是降低了 alpha 值；
-   3. 子元素设置 opacity:1 显示无效；
-   4. 有过渡动画效果；

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

### 布局 - 自适应宽

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
.clear-fix::after{
    content: '';
    display: block;
    clear: both;
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
    <div class="container clear-fix">
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

### 布局 - 自适应高

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

### 布局 - 后台布局

```
.container{
    position: fixed;
    width: 100%;
    height: 100%;
}
.header{
    height: 60px;
    background-color: #ccc;

    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
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

### 文字阴影

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

### clip-path

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

## linear-gradient

linear adj.线性的; gradient n.斜坡; adj.倾斜的;

background: 可多个 linear-gradient 函数，与属性名越近的层级越高，图层越在上面；
