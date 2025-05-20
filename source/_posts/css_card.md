---
title: "css卡片"
---

![](/images/css/original/css_card.png)

## 3D 立体卡片

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
    background-color: #ededed;
  }
  .container {
    margin: 50px;
    position: relative;
    width: 1050px;
    display: flex;
    flex-wrap: wrap;
    gap: 50px;

    /* 整体绕Y倾斜10度 */
    transform: skewY(-10deg);
  }
  .container .card {
    position: relative;
    width: 300px;
    height: 400px;
    background-color: #fff;
    transition: 0.5s;
  }
  /* 设置card顶部, 实现立体感 */
  .container .card::before {
    content: "";
    position: absolute;
    top: -15px;
    left: 0;
    width: 100%;
    height: 15px;
    background-color: #00c0f6;
    background-color: yellowgreen;

    transform-origin: bottom;
    transform: skewX(45deg);
    transition: 0.5s;
  }
  /* 设置card左侧, 实现立体感 */
  .container .card::after {
    content: "";
    position: absolute;
    top: -15px;
    left: -15px;
    width: 15px;
    height: 50%;
    background-color: #00c0f6;
    background-color: red;
    transition: 0.5s;

    border-bottom: 200px solid #d9d9d9;
    transform-origin: left;
    transform: skewY(45deg);
  }
  .container .card:hover {
    transform: translateY(-40px);
  }

  .container .card .img-box {
    position: relative;
    width: 300px;
    height: 200px;
    background-color: #00c7ff;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
  }
  .container .card .img-box img {
    max-width: 100px;
  }
  .container .card .img-box h3 {
    position: relative;
    color: #fff;
    margin-top: 10px;
  }
  .container .card .content {
    position: relative;
    width: 100%;
    height: 200px;
    padding: 20px;
    color: #777;
    text-align: center;
    background-color: #fff;
  }

  /* 设置阴影 */
  .container .card .content::before {
    content: "";
    position: absolute;
    left: 0;
    bottom: 0;
    width: 100%;
    height: 400px;
    background-image: linear-gradient(
      transparent,
      transparent,
      rgba(0, 0, 0, 0.1)
    );
    transform-origin: bottom;
    transform: skewX(45deg);
    transition: 0.5s;
    pointer-events: none;
    z-index: -1;
  }
  /* 设置卡片hover时的阴影 */
  .container .card:hover .content::before {
    transform: translateY(40px) skewX(45deg);
    filter: blur(5px);
    opacity: 0.5;
  }

  /* 解决阴影层叠问题 */
  .container .card:nth-child(1) {
    z-index: 3;
  }
  .container .card:nth-child(2) {
    z-index: 2;
  }
  .container .card:nth-child(3) {
    z-index: 1;
  }
</style>

<div class="container">
  <div class="card">
    <div class="img-box">
      <img src="https://picsum.photos/200/300" alt="" />
      <h3>设计</h3>
    </div>
    <div class="content">
      <p>
        Lorem ipsum dolor sit amet consectetur, adipisicing elit. Aut, dolorem!
      </p>
    </div>
  </div>

  <div class="card">
    <div class="img-box">
      <img src="https://picsum.photos/200/300" alt="" />
      <h3>设计</h3>
    </div>
    <div class="content">
      <p>
        Lorem ipsum dolor sit amet consectetur, adipisicing elit. Aut, dolorem!
      </p>
    </div>
  </div>
</div>
```

## 堆叠卡片

![](/images/css/original/css_stack_card.gif)

```html
<head>
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
      background-color: orange;
    }

    .card-wrapper .card {
      position: relative;
      width: 300px;
      padding: 15px;
      border-radius: 25px;
      margin-bottom: 10px;
      box-shadow: 3px 3px 3px rgba(0, 0, 0, 0.1);
      border: 1px solid rgba(255, 255, 255, 0.4);

      display: flex;
      align-items: end;
      justify-content: space-between;

      /* 背景模糊，需背景颜色有透明才能看到效果 */
      backdrop-filter: blur(30px);
      background-color: rgba(255, 255, 255, 0.228);

      /* transition: all 0.2s cubic-bezier(.68,-0.55,.27,0.027); */
      transition: all 0.2s ease-in-out;
    }

    .card.active {
      transform: unset;
    }

    /* 渲染时就移动位置; 当被active时，清除transform回到原本html结构中位置 */
    .card-1 {
      z-index: 2;
      transform: translateY(200%) scale(0.95);
    }
    .card-2 {
      z-index: 1;
      transform: translateY(100%) scale(0.9);
    }
    .card-3 {
      z-index: 0;
      transform: translateY(0px) scale(0.85);
    }

    .card-left {
      position: relative;
      display: flex;
    }
    .card-left img {
      width: 50px;
      height: 50px;
      border-radius: 50%;
    }
    .card-left .content {
      position: relative;
      margin: 3px 0 0 10px;
    }
    .card-left .content h2 {
      position: relative;
      margin: 0;
      font-size: 16px;
      font-weight: 600;
    }
    .card-left .content span {
      position: relative;
      color: #f7efef;
      font-size: 14px;
    }

    .card-wrapper .btn-group {
      position: relative;
      display: flex;
      align-items: center;
      justify-content: center;
    }
    .card-wrapper .btn-group .btn {
      position: relative;
      padding: 10px 40px 10px 30px;
      background-color: #fffff3;
      border-radius: 20px;
      border: 0;
      backdrop-filter: blur(5px);
      box-shadow: 0px 1px 2px #9e9d9d;
      font-size: 15px;
      font-weight: 600;
      cursor: pointer;
      color: #444;
    }

    /* 设置三角形 */
    .card-wrapper .btn-group .btn::after {
      content: "";
      position: absolute;
      border-top: 2px solid #616160;
      border-left: 2px solid #5e5a5a;
      width: 7px;
      height: 7px;
      right: 23px;
      top: 12px;
      transform: rotate(225deg);
      transition: all 0.2s linear;
    }
    .card-wrapper .btn-group .btn.active::after {
      transform: rotate(45deg);
      top: 17px;
    }
  </style>
</head>

<body>
  <div class="card-wrapper">
    <div class="card card-1">
      <div class="card-left">
        <img src="https://picsum.photos/50/50?random=1" alt="" />
        <div class="content">
          <h2>title1</h2>
          <span>contents</span>
        </div>
      </div>
      <div class="card-right">2048-1-1</div>
    </div>

    <div class="card card-2">
      <div class="card-left">
        <img src="https://picsum.photos/50/50?random=2" alt="" />
        <div class="content">
          <h2>title2</h2>
          <span>contents</span>
        </div>
      </div>
      <div class="card-right">2048-1-2</div>
    </div>

    <div class="card card-3">
      <div class="card-left">
        <img src="https://picsum.photos/50/50?random=3" alt="" />
        <div class="content">
          <h2>title3</h2>
          <span>contents</span>
        </div>
      </div>
      <div class="card-right">2048-1-3</div>
    </div>

    <div class="btn-group">
      <button class="btn">展开</button>
    </div>
  </div>

  <script>
    const btn = document.querySelector(".btn");
    const cards = document.querySelectorAll(".card");
    let flag = true;

    btn.addEventListener("click", function () {
      // 按钮切换状态
      this.classList.toggle("active");

      if (flag) {
        flag = false;
        this.textContent = "收起";
        cards.forEach(function (item) {
          item.classList.add("active");
        });
      } else {
        flag = true;
        this.textContent = "展开";
        cards.forEach(function (item) {
          item.classList.remove("active");
        });
      }
    });
  </script>
</body>
```
