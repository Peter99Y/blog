---
title: "svg"
---

## svg

Scalable Vector Graphics 是基于 xml 语法的图像格式, 可缩放矢量图；

| 属性           | 含义               |
| -------------- | ------------------ |
| width          | 宽度               |
| height         | 高度               |
| fill           | 填充颜色           |
| opacity        | 整个元素的不透明度 |
| fill-opacity   | 填充不透明度       |
| stroke-width   | 边框宽度           |
| stroke         | 边框颜色           |
| stroke-opacity | 边框不透明度       |

## rect

| 属性 | 含义                        |
| ---- | --------------------------- |
| x    | 元素距离左上角 x 轴起始坐标 |
| y    | 元素距离左上角 y 轴起始坐标 |
| rx   | x 轴的半径                  |
| ry   | y 轴的半径                  |

``` javascript
    <svg width="200" height="110">
      <rect
        x="5"
        y="5"
        width="190"
        height="100"
        fill="transparent"
        stroke-width="5"
        stroke="black"
      />
    </svg>

    <svg width="110" height="110">
      <rect
        x="5"
        y="5"
        rx="10"
        ry="10"
        width="100"
        height="100"
        fill="blue"
        stroke-width="5"
        stroke="tomato"
        fill-opacity="0.5"
        stroke-opacity="0.5"
      />
    </svg>
```

![](/images/svg/rect.png)

---

## circle

| 属性 | 含义              |
| ---- | ----------------- |
| r    | 圆形的半径        |
| cx   | 圆形中心的 x 坐标 |
| cy   | 圆形中心的 y 坐标 |

```
    <svg width="400" height="180">
      <rect
        rx="20"
        ry="20"
        x="50"
        y="20"
        width="150"
        height="150"
        fill="blue"
        stroke-width="3"
        stroke="tomato"
        fill-opacity="0.5"
        stroke-opacity="0.5"
      />
    </svg>

    <br />

    <svg width="100" height="180">
      <circle
        cx="50"
        cy="50"
        r="40"
        stroke="black"
        stroke-width="3"
        fill="red"
      />
    </svg>
```

## ellipse

| 属性 | 含义          |
| ---- | ------------- |
| cx   | 圆心的 x 坐标 |
| cy   | 圆心的 y 坐标 |
| rx   | 水平半径      |
| ry   | 垂直半径      |

```
    <svg width="200" height="100">
      <!-- 圆心在100和50位置；X轴半径95*2+边框5*2 = 200，y轴半径45*2+边框5*2 = 100-->
      <ellipse
        cx="100"
        cy="50"
        rx="95"
        ry="45"
        fill="transparent"
        stroke="black"
        stroke-width="5"
      />
    </svg>

    <svg width="200" height="100">
      <ellipse cx="100" cy="40" rx="100" ry="30" fill="purple" />
      <ellipse cx="100" cy="30" rx="80" ry="20" fill="lime" />
      <ellipse cx="100" cy="20" rx="60" ry="10" fill="orange" />
    </svg>

    <svg width="200" height="100">
      <ellipse cx="100" cy="50" rx="100" ry="30" fill="green" />
      <ellipse cx="100" cy="50" rx="90" ry="20" fill="white" />
    </svg>
```

![](/images/svg/ellipse.png)

---

## line & polyline

| 属性 | 含义                  |
| ---- | --------------------- |
| x1   | 直线的 x 轴的起点坐标 |
| y1   | 直线的 y 轴的起点坐标 |
| x2   | 直线的 x 轴的结束坐标 |
| y2   | 直线的 y 轴的结束坐标 |

```
    <svg width="200" height="200">
      <line x1="0" y1="0" x2="160" y2="120" stroke-width="2" stroke="red" />
    </svg>

    <svg width="200" height="200">
      <polyline
        points="0,0, 40,0 40,40 80,40 80,80 120,80 120,120 160,120"
        fill="none"
        stroke-width="3"
        stroke="red"
      />
    </svg>
```

![](/images/svg/line.png)

---

## golygon

最后一个点会自动封闭

| 属性   | 含义                     |
| ------ | ------------------------ |
| points | 描述多个点坐标，空格隔开 |

```
    <svg width="200" height="200">
      <polygon
        points="100,0, 150,100 50,100"
        fill="yellow"
        stroke-width="2"
        stroke="black"
      />
    </svg>

    <svg width="200" height="200">
      <polygon
        points="100,10 40,198 198,78 10,78 160,198"
        fill="yellow"
        stroke-width="2"
        stroke="black"
      />
    </svg>
```

![](/images/svg/polygon.png)

---

## text

| 属性        | 含义                                                               |
| ----------- | ------------------------------------------------------------------ |
| x           | 文字的 x 轴坐标                                                    |
| y           | 文字的 y 轴坐标                                                    |
| font-size   | 文字大小                                                           |
| text-anchor | 水平对齐方式 start/middle/end (文字起始的位置是 基于 x 起始坐标点) |
| transform   | rotate(deg x,y)                                                    |

```
    <svg width="100" height="100">
      <text x="50" y="12" fill="red" font-size="12" text-anchor="start">svg</text>
    </svg>

    <svg width="100" height="100">
      <text x="0" y="12" fill="red">
        SVG
        <tspan x="10" y="40" fill="orange">first</tspan>
        <tspan x="10" y="60" fill="green">second</tspan>
      </text>
    </svg>

    <svg width="100" height="100" xmlns:xlink="https://www.w3.org/1999/xlink">
      <a xlink:href="https://www.baidu.com" target="_blank">
        <text x="0" y="15" fill="blue">link</text>
      </a>
    </svg>
```

![](/images/svg/text.png)

## path

| 属性       | 含义                       |
| ---------- | -------------------------- |
| M (moveto) | 大写绝对定位，基于坐标原点 |
| l (lineto) | 小写相对定位，基于上一个点 |
| q          | 绘制贝塞尔曲线             |
