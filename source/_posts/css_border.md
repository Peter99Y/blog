---
title: css原生边框动画
---

<div style="display:flex;justify-content: center; gap: 50px;">

![](/images/css/original/css_border.png)

![](/images/css/original/css_border2.png)

</div>

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
    background-color: #ededed;
  }
  .box {
    position: relative;
    width: 200px;
    height: 300px;
    background: rgba(0, 0, 0, 0.5);
    overflow: hidden;
  }
  /* 设置2个动画(也4个边框)，如只需一个只设置一个伪类 */
  .box::after,
  .box::before {
    content: "";
    position: absolute;
    width: 60%;
    height: 130%;
    top: 50%;
    left: 50%;
    translate: -50% -50%;
    background: linear-gradient(315deg, #00ccff, #d400d4);
    animation: animate 8s linear infinite;
  }
  /* 设置第二个动画颜色和延迟执行 */
  .box::after {
    background-color: liner-gradient(315deg, #ffeb4d, #e91e63);
    animation-delay: -2s;
  }
  @keyframes animate {
    0% {
      transform: rotate(0deg);
    }
    100% {
      transform: rotate(360deg);
    }
  }

  /* hover时效果 */
  .box:hover::after,
  .box:hover::before {
    animation-play-state: paused;
  }

  /* 覆盖整个box，inset就是边框动画的宽度 */
  .box .content {
    position: absolute;
    inset: 8px;
    background-color: orange;
    z-index: 1;
    color: #fff;
  }
</style>

<div class="box">
  <div class="content">
    <h2>border</h2>
  </div>
</div>
```
