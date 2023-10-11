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

### 伪类

###### :has

通过子元素设置父元素样式;

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

###### ::before/::after

-   attr 通过元素上的属性值设置 content

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
