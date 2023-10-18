---
title: Css
---

### 包含块

-   元素的排列是在一块区域中，这个区域包含了这个元素，所以元素的包含块就是元素的排列区域；

-   浮动元素 or 普通元素的包含块就是父元素的 width 内容区域(不包含父元素 padding、border、margin 宽度)；元素的 width/height 的**百分比**宽度就是相对于父元素的 width/height；

-   绝对定位元素的包含块是带有定位元素 padding+width 区域；绝对定位元素的 left/top 是相对于 padding+width 区域；

### 粘性布局

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

### 伪元素 ::before/::after

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

### 伪类

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

### display

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

### visibility

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
