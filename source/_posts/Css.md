---
title: Css
---

#### :has - 伪类

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
