---
title: "css原生悬停滑块滑动"
---

![](/images/css/original/css_slide.gif)

![](/images/css/original/css_slide2.png)

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

---

![](/images/css/original/css_slide3.png)

```html
<style>
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }
  :root {
    --slide-width: 240px;
    --slide-margin: 50px;
  }
  body {
    height: 100%;
    background-color: orange;
  }

  .slider-wrapper {
    margin: 100px auto;
    width: 80%;
    height: 600px;
    position: relative;
    border: 1px solid black;
  }
  .slider {
    background-color: #ccc;
    border-radius: 20px;

    /* inset: 80px 300px 80px 80px; */
    position: absolute;
    top: 80px;
    right: 300px;
    bottom: 80px;
    left: 80px;
  }
  .slider .slides {
    width: var(--slide-width);
    height: 300px;
    border-radius: 20px;
    box-shadow: 0 25px 50px rgba(0, 0, 0, 0.5);
    transition: 0.5s;

    background: var(--img);
    background-size: cover;
    background-position: center;

    /* 垂直居中 */
    position: absolute;
    transform: translateY(-50%);
    top: 50%;
  }
  /* 
      第2张覆盖第1张，默认就是从第2张开始;
      点击下一张时，第1张剪切到最后一张时，会有渲染时间导致空白有断层感
    */
  .slider .slides:nth-child(1),
  .slider .slides:nth-child(2) {
    position: absolute;
    left: 0;
    top: 0;

    /* 前2张的宽高和父级容器相同 */
    width: 100%;
    height: 100%;
    transform: translateY(0);
    box-shadow: 0 25px 50px rgba(0, 0, 0, 0.2);
    background-size: cover;
  }

  /* 前2张的宽高和父级容器相同, 其他图片是默认的width:240px; height:320px; */
  .slider .slides:nth-child(3) {
    left: calc(50% + var(--slide-width));
  }
  .slider .slides:nth-child(4) {
    left: calc(50% + var(--slide-width) * 2 + var(--slide-margin));
    /* left: calc(50% + var(--slide-width) + var(--slide-margin)); */
  }
  .slider .slides:nth-child(5) {
    left: calc(50% + var(--slide-width) * 3 + var(--slide-margin) * 2);
    /* left: calc(50% + var(--slide-width) + var(--slide-margin) * 2); */
  }
  .slider .slides:nth-child(6) {
    left: calc(50% + var(--slide-width) * 4 + var(--slide-margin) * 3);
    /* left: calc(50% + var(--slide-width) + var(--slide-margin) * 3); */
    opacity: 0;
  }

  .btn {
    width: 100%;
    position: absolute;
    bottom: 15px;
    display: flex;
    justify-content: center;
    gap: 20px;
  }
  .btn > div {
    width: 50px;
    height: 50px;
    border-radius: 50%;
    color: #fff;
    font-size: 14px;
    background-color: #111;

    cursor: pointer;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  /* 设置响应式布局 */
  @media (max-width: 900px) {
    .slider {
      inset: 40px 40px 200px 40px;
    }
    .slider .slides {
      width: 100px;
      height: 100px;
      top: initial;
      bottom: -170px;
      box-shadow: 0 10px 20px rgba(0, 0, 0, 0.2);
    }
    .slider .slides:nth-child(1),
    .slider .slides:nth-child(2) {
      top: initial;
      bottom: 0;
    }
    .slider .slides:nth-child(3) {
      left: 0;
    }
    .slider .slides:nth-child(4) {
      left: 120px;
    }
    .slider .slides:nth-child(5) {
      left: 240px;
    }
    .slider .slides:nth-child(6) {
      left: 360px;
    }
  }
</style>

<body>
  <div class="slider-wrapper">
    <div class="slider">
      <div
        class="slides"
        style="--img: url('https://picsum.photos/240/300?random=1')"
      ></div>
      <div
        class="slides"
        style="--img: url('https://picsum.photos/240/300?random=2')"
      ></div>
      <div
        class="slides"
        style="--img: url('https://picsum.photos/240/300?random=3')"
      ></div>
      <div
        class="slides"
        style="--img: url('https://picsum.photos/240/300?random=4')"
      ></div>
      <div
        class="slides"
        style="--img: url('https://picsum.photos/240/300?random=5')"
      ></div>
      <div
        class="slides"
        style="--img: url('https://picsum.photos/240/300?random=6')"
      ></div>
    </div>

    <div class="btn">
      <div class="prev">上</div>
      <div class="next">下</div>
    </div>
  </div>

  <script>
    const prev = document.querySelector(".prev");
    const next = document.querySelector(".next");
    const slider = document.querySelector(".slider");

    next.addEventListener("click", () => {
      let slides = slider.querySelectorAll(".slides");
      slider.appendChild(slides[0]);
    });

    prev.addEventListener("click", () => {
      let slides = slider.querySelectorAll(".slides");
      slider.prepend(slides[slides.length - 1]);
    });
  </script>
</body>
```
