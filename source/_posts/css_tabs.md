---
title: "css原生tabs菜单栏"
---

![](/images/css/original/tabs.png)

```html
<style>
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }
  body {
    height: 100vh;
    background-color: orange;
  }
  :root {
    --color-primary: #a12db5;
  }
  .container {
    margin-top: 50px;
    margin-left: 50px;
    display: flex;
    align-items: center;
    width: 600px;
    height: 330px;
  }
  .container .tabs-wrapper {
    width: 80px;
    padding: 40px 0;
    position: relative;
  }
  /* 隐藏原生input 标签 */
  .tabs-wrapper input {
    display: none;
  }

  /* 左侧label 不透明度切换效果 */
  .tabs-wrapper input:nth-child(1):checked ~ .icons label:nth-child(1) {
    opacity: 1;
  }
  .tabs-wrapper input:nth-child(2):checked ~ .icons label:nth-child(2) {
    opacity: 1;
  }
  .tabs-wrapper input:nth-child(3):checked ~ .icons label:nth-child(3) {
    opacity: 1;
  }

  /* 左侧指示器 位置切换移动效果 */
  .tabs-wrapper input:nth-child(1):checked ~ .icons .indicator {
    top: 0;
  }
  .tabs-wrapper input:nth-child(2):checked ~ .icons .indicator {
    top: 33%;
  }
  .tabs-wrapper input:nth-child(3):checked ~ .icons .indicator {
    top: 66%;
  }

  /* 右侧内容 切换展示 */
  .tabs-wrapper
    input:nth-child(1):checked
    ~ .content
    .content-box:nth-child(1) {
    opacity: 1;
  }
  .tabs-wrapper
    input:nth-child(2):checked
    ~ .content
    .content-box:nth-child(2) {
    opacity: 1;
  }
  .tabs-wrapper
    input:nth-child(3):checked
    ~ .content
    .content-box:nth-child(3) {
    opacity: 1;
  }

  .tabs-wrapper .icons {
    display: flex;
    flex-direction: column;
    position: relative;
  }
  .tabs-wrapper .icons label {
    width: 80px;
    height: 80px;
    line-height: 80px;
    font-size: 30px;
    font-weight: bold;
    color: #fff;
    text-align: center;
    opacity: 0.5;
    z-index: 2;
  }

  /* 左侧指示器 */
  .tabs-wrapper .icons .indicator {
    position: absolute;
    left: 0;
    top: 0;
    width: 80px;
    height: 80px;
    background-color: var(--color-primary);
    border-top-left-radius: 30px;
    border-bottom-left-radius: 30px;
    transition: all 0.2s;
  }
  .tabs-wrapper .icons .indicator::before {
    content: "";
    position: absolute;
    right: 0;
    top: -20px;
    width: 20px;
    height: 20px;
    /* background-color: red; 打开查看非透明情况 */
    /* box-shadow: 8px 8px 0 8px blue; */
    background-color: transparent;
    box-shadow: 8px 8px 0 8px var(--color-primary);
    border-bottom-right-radius: 30px;
  }
  .tabs-wrapper .icons .indicator::after {
    content: "";
    position: absolute;
    right: 0;
    bottom: -20px;
    width: 20px;
    height: 20px;
    /* background-color: red; 打开查看非透明情况 */
    /* box-shadow: 8px -8px 0 8px blue; */
    background-color: transparent;
    box-shadow: 8px -8px 0 8px var(--color-primary);
    border-top-right-radius: 30px;
  }

  /* 右侧 */
  .tabs-wrapper .content {
    position: absolute;
    width: 300px;
    height: 330px;
    top: 0;
    /* left是根据左侧label的宽度 */
    left: 80px;
    background: linear-gradient(90deg, var(--color-primary), #df0d54);
    border-radius: 20px;
    color: #fff;
  }
  .tabs-wrapper .content .content-box {
    position: absolute;
    inset: 0;
    padding: 20px;
    opacity: 0;
    transition: 0.2s;
  }
</style>

<div class="container">
  <div class="tabs-wrapper">
    <input type="radio" id="home" name="group" checked />
    <input type="radio" id="user" name="group" />
    <input type="radio" id="setting" name="group" />

    <div class="icons">
      <label for="home">h</label>
      <label for="user">u</label>
      <label for="setting">s</label>
      <div class="indicator"></div>
    </div>

    <div class="content">
      <div class="content-box">home内容</div>
      <div class="content-box">user内容</div>
      <div class="content-box">setting内容</div>
    </div>
  </div>
</div>
```

---

![](/images/css/original/tabs2.gif)

```html
<style>
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }
  :root {
    --color-primary: #c7f80d;
    --gb-color: #55650a;
    --common-color: #161616;
  }
  body {
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    background-color: var(--common-color);
  }
  ul {
    position: relative;
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 10px;
    padding: 10px 60px;
    background-color: #242424;
    border-radius: 30px;
  }
  ul li {
    list-style: none;
  }
  ul li a {
    position: relative;
    text-decoration: none;
    display: flex;
    justify-content: center;
    align-items: center;
    width: 80px;
    height: 80px;
    z-index: 1000;
    color: #444;
  }
  /* 图标大小 */
  ul li a ion-icon {
    font-size: 30px;
    transition: all 0.2s ease;
  }
  ul li a span {
    transform: translateY(40px);
    position: absolute;
    font-size: 14px;
  }
  ul li:hover a span {
    color: var(--color-primary);
    filter: drop-shadow(0 0 15px var(--color-primary));
  }
  ul li.active a span {
    color: var(--color-primary);
  }
  ul li:hover a ion-icon {
    color: var(--color-primary);
    filter: drop-shadow(0 0 15px var(--color-primary));
  }
  ul li.active a ion-icon {
    color: var(--color-primary);
    transform: translateY(-50px);
    /* 设置投影 */
    filter: drop-shadow(0 0 15px var(--color-primary));
  }

  /* 顶部绿块 */
  #mark {
    width: 80px;
    height: 80px;
    border-radius: 20px;
    background-color: var(--gb-color);
    border-top: 2px solid var(--color-primary);
    border-left: 2px solid var(--color-primary);
    transition: 0.2s ease;

    /* -40是移动方块一半高度 */
    position: absolute;
    top: -40px;

    /* 绿块透明边距 */
    /* 第一列是透明边距, 第二列是制作绿块下方反射效果 */
    box-shadow: 0 0 0 10px var(--common-color);
    box-shadow: 0 0 0 10px var(--common-color), 0 10px 10px var(--color-primary);
  }
  /* 绿块左侧小块 */
  #mark:before {
    content: "";
    width: 20px;
    height: 20px;
    position: absolute;
    top: 38px;
    left: -32px;
    /* background-color: red; */
    background-color: transparent;

    /* 遮挡因border-top-right-radius所露出来的部分 */
    box-shadow: 4px -4px 0 4px var(--common-color);
    border-top-right-radius: 20px;
  }
  /* 绿块右侧小块 */
  #mark:after {
    content: "";
    width: 20px;
    height: 20px;
    position: absolute;
    top: 38px;
    right: -30px;
    /* background-color: red; */
    background-color: transparent;


    /* 遮挡因border-top-left-radius所露出来的部分 */
    box-shadow: -4px -4px 0 4px var(--common-color);
    border-top-left-radius: 20px;
  }

  /* 制作底部椭圆bar */
  #mark .ellipse {
    position: absolute;
    top: 112px;
    width: 120%;
    left: -10%;
    height: 30px;
    background-color: var(--gb-color);
    border-radius: 20px;

    /* 第一列是透明边距, 第二列是椭圆上方反射效果 */
    box-shadow: 0 0 0 10px var(--common-color);
    box-shadow: 0 0 0 10px var(--common-color), 0 -2px 0 9px var(--color-primary);

    border-left: 1px solid var(--color-primary);
    border-right: 1px solid var(--color-primary);
  }
</style>

<body>
  <ul>
    <li>
      <a href="#">
        <ion-icon name="boat-outline"></ion-icon>
        <span>home</span>
      </a>
    </li>
    <li class="active">
      <a href="#">
        <ion-icon name="people-outline"></ion-icon>
        <span>user</span>
      </a>
    </li>
    <li>
      <a href="#">
        <ion-icon name="cog-outline"></ion-icon>
        <span>setting</span>
      </a>
    </li>

    <div id="mark">
      <div class="ellipse"></div>
    </div>
  </ul>

  <script>
    const mark = document.getElementById("mark");
    const list = document.querySelectorAll("ul li");

    list.forEach((item, index) => {
      item.addEventListener("click", () => {
        list.forEach((it) => it.classList.remove("active"));
        item.classList.add("active");
        mark.style.left = item.offsetLeft + "px";
        mark.style.width = item.offsetWidth + "px";
      });
    });
  </script>
</body>
```

> mark:before/after作用

<div style="display: flex; gap: 10px;">

![](/images/css/original/tabs3.png)

![](/images/css/original/tabs4.png)

</div>
