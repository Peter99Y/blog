---
title: "css原生悬停滑块滑动"
---

![](/images/css/original/css_slide.gif)

```html
<style>
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }
  body {
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    background-color: #28272a;
  }
  .btn {
    position: relative;
    width: 200px;
    height: 60px;
    letter-spacing: 2px;
    border-radius: 60px;
    color: rgba(255, 255, 255, 0.25);
    background-color: rgba(255, 255, 255, 0.1);
    text-decoration: none;

    /* 取消隐藏可查看细节 */
    overflow: hidden;

    display: flex;
    justify-content: center;
    align-items: center;
    border-top: 1px solid rgba(255, 255, 255, 0.35);
    border-left: 1px solid rgba(255, 255, 255, 0.35);

    transition: 0.5s;
    padding-left: 40px;
    padding-right: 0;
  }
  /* 动效1: 左减少、右增加padding内容产生移动效果 */
  .btn:hover {
    padding-left: 0;
    padding-right: 40px;
    color: rgba(255, 255, 255, 1);
  }

  .btn div {
    position: absolute;
    left: 5px;
    width: 50px;
    height: 50px;
    background-color: #04fe4d;
    border-radius: 50%;
    transition: 0.5s ease-in-out;
    color: #000;
    font-size: 2.5em;
    text-align: center;
  }
  /* 动效2：球体往右移动 */
  .btn:hover div {
    left: calc(100% - 55px);
  }

  .btn:after {
    content: "";
    position: absolute;
    width: 80px;
    height: 100%;
    z-index: 1;
    background-color: rgba(255, 255, 255, 0.5);
    transform: translateX(-170px) skewX(30deg);

    /* 比球体移动稍慢一点 */
    transition: 0.75s ease-in-out;
  }
  /* 动效3： */
  .btn:hover:after {
    transform: translateX(170px) skewX(30deg);
  }
</style>

<body>
  <a href="#" class="btn">
    <div><ion-icon name="arrow-forward-outline"></ion-icon></div>
    next step
  </a>
</body>
```

- 移除 overflow:hidden;

![](/images/css/original/css_slide2.png)
