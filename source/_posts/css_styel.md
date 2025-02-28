---
title: "元素尺寸"
---

### dom.style

只能读取行内样式中的样式值;

### getComputedStyle

获取元素所有样式属性，包括行内样式和样式表的样式值;

### dom.offsetLeft / dom.offsetTop

![元素偏移量](/images/Dom&Bom/5.webp)

### dom.offsetWidth

width + padding + border;
(包含了可能会出现滚动条的宽高)

### dom.clientWidth

width + padding;
(不包含出现的滚动条的宽高)
(dom.clientLeft 获取元素左侧 border 宽度)

### dom.scrollWidth

width + padding
会包含父元素设置了超出了边界而隐藏的宽度
内容大小没有超出元素大小时，也就是没有滚动条时 = clientWidth

```
获取带有定位的父级元素，否则相对于body元素
console.log(div.offsetParent)    // <body></body>

不管父级有没有定位,返回最近的父级元素
console.log(div.parentNode)
```

![scrollWidth](/images/Dom&Bom/4.webp)

### dom.getBoundingClientRect

获取元素大小，以及相对于视口位置；
获取元素变换过后的尺寸，包括 transform 属性的变化