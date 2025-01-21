---
title: keyFrames
---

###### perspective

```html
<style>
  * {
    margin: 0;
    padding: 0;
  }
  body {
    width: 100vw;
    height: 100vh;
    background-color: #363549;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }

  .wrapper {
    width: 200px;
    height: 300px;
    border: 2px solid orange;
    perspective: 200px;
  }

  .wrapper .shape {
    background-color: yellowgreen;
    width: 200px;
    height: 300px;

    transform-origin: center bottom;
    transition: all 1s ease;
  }

  .wrapper:hover .shape {
    transform: rotateX(45deg);
  }
</style>

<body>
  <div class="wrapper">
    <div class="shape"></div>
  </div>
</body>
```

![](/images/animation/6.gif)

```html
<style>
  div {
    width: 100px;
    height: 100px;

    /* 如果这里不预设背景色，动画开始前和结束后都没有背景色，只是看不见而已 */
    background-color: #fff;

    animation-delay: 2s;
    animation-name: h;
    animation-duration: 2s;
  }

  /* 执行逻辑：盒子白色2秒，突变为红色，在2秒之间渐变为绿色，，动画执行完突变为默认的白色； */
  @keyframes h {
    0% {
      background-color: red;
    }
    100% {
      background-color: green;
    }
  }
</style>
```

###### animation-fill-mode

> 动画在执行之前和之后的状态

- 默认：保持 2 秒初始状态；大小突变为 0，背景红色，渐变为绿色，大小渐变为 2 倍；2 秒结束后，突变为初始大小和初始颜色；

- backwards(以起始帧为初始状态)：大小从 0，背景红色保持延迟 2 秒，2 秒后渐变为结束状态，动画结束后，突变为初始大小和初始颜色；

- forwards(以默认为初始状态，以结束帧为结束状态)：保持 2 秒初始状态；大小突变为 0，背景红色，执行动画渐变为绿色，大小渐变为 2 倍；并保持为结束状态；

- both (以起始帧为默认状态，以结束帧为结束状态)：大小从 0，背景红色，保持 2 秒后执行动画；渐变为绿色，大小渐变为 2 倍；并保持为结束状态；

```html
<style>
  div {
    width: 100px;
    height: 100px;
    background-color: orange;

    animation-name: h;
    animation-duration: 2s;
    animation-delay: 2s;
  }
  .backwards {
    animation-fill-mode: backwards;
  }
  .forwards {
    animation-fill-mode: forwards;
  }
  .both {
    animation-fill-mode: both;
  }
  @keyframes h {
    0% {
      background-color: red;
      transform: scale(0);
    }
    100% {
      background-color: green;
      transform: scale(1);
    }
  }
</style>
```

---

```html
<style>
  .father {
    width: 400px;
    height: 400px;
    border: 1px solid #fff;
  }

  .child {
    width: 100px;
    height: 100px;
    background-color: orange;

    animation-name: h;
    animation-duration: 1s;
  }

  @keyframes h {
    /* 0% {
          transform: translate(0, 0);
        } */
    25% {
      transform: translate(300px, 0);
    }
    50% {
      transform: translate(300px, 300px);
    }
    75% {
      transform: translate(0, 300px);
    }
    /* 100% {
          transform: translate(0, 0);
        } */
  }
</style>
```

![](/images/animation/1.gif)

```html
<style>
  .heart::before {
    content: "";
    width: 100px;
    height: 100px;
    border-radius: 50%;
    position: absolute;
    background-color: #e74c3c;
    transform: translateX(-50%);
  }
  .heart::after {
    content: "";
    width: 100px;
    height: 100px;
    border-radius: 50%;
    position: absolute;
    background-color: #e74c3c;
    transform: translateY(-50%);
  }

  .heart {
    width: 100px;
    height: 100px;
    background-color: #e74c3c;

    transform: rotate(45deg) scale(0.5);
    opacity: 0.5;

    animation-name: h;
    animation-duration: 2s;
    animation-iteration-count: infinite;
  }

  @keyframes h {
    25% {
      transform: rotate(45deg) scale(1);
      opacity: 1;
    }
    50% {
      transform: rotate(45deg) scale(0.5);
      opacity: 0.5;
    }
    75% {
      transform: rotate(45deg) scale(1);
      opacity: 1;
    }
    100% {
      transform: rotate(45deg) scale(0.5);
      opacity: 0.5;
    }
  }
</style>
```

![](/images/animation/2.gif)

###### animation-direction

> 正向播放、反向播、正向和反向之间交替播放。

- normal：元素默认状态执行动画，执行完动画后突变为**默认状态**；
- reverse: 元素从动画的最后帧开始执行动画，执行完动画后突变为**默认状态**，不是重新回到初始帧；
- alternate: 元素从默认状态执行动画 (animation-iteration-count 为 1 次)，再从最后帧执行动画到默认状态(需 animation-iteration-count 为 2 次)，执行完自然就是默认状态；；
- alternate-reverse: 元素从动画最后帧开始执行 (animation-iteration-count 为 1 次)，再从默认状态执行到最后帧 (需 animation-iteration-count 为 2 次)，执行完最后突变为默认状态；

```html
<style>
  * {
    margin: 0;
    padding: 0;
  }
  body {
    width: 100vw;
    height: 100vh;
    background-color: #363549;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }
  .wrapper {
    position: relative;
  }
  /* 小球 */
  .box {
    width: 100px;
    height: 100px;
    border-radius: 50%;
    background-color: #e67e22;

    animation-name: bounce;
    animation-duration: 1s;
    transition-timing-function: ease-out;
    animation-iteration-count: infinite;
  }
  .box4 {
    animation-direction: alternate-reverse;
  }
  @keyframes bounce {
    0% {
      transform: scale(1.2, 0.8);
    }
    100% {
      transform: translateY(-300px) scale(0.8, 1.2);
    }
  }

  /* 阴影 */
  section {
    background-color: rgba(0, 0, 0, 0.3);
    filter: blur(5px);
    width: 100px; /* 与小球宽度一致 */
    height: 40px;
    border-radius: 50%;
    transform: translateY(-20px);
    position: absolute;
    z-index: -1; /* 阴影再小球后面 */

    animation-name: shadow;
    animation-duration: 1s;
    animation-iteration-count: infinite;
    animation-direction: alternate-reverse;
  }
  @keyframes shadow {
    100% {
      filter: blur(35px);
      background-color: rgba(0, 0, 0, 0.1);
    }
  }
</style>
```

![](/images/animation/3.gif)

###### animation-play-state

```html
<style>
  button {
    width: 120px;
    height: 50px;
    background-color: #fff;
    color: orange;
    border: solid 1px orange;
  }
  button::after {
    content: "";
    width: 3px;
    height: 3px;
    display: inline-block;
    box-shadow: 3px 0 0 orange, 9px 0 0 orange, 15px 0 0 orange;

    animation-name: h;
    animation-duration: 1s;
    animation-iteration-count: infinite;
    animation-play-state: paused;
  }
  button:hover::after {
    animation-play-state: running;
  }

  @keyframes h {
    from {
      box-shadow: none;
    }
    30% {
      box-shadow: 3px 0 0 orange;
    }
    60% {
      box-shadow: 3px 0 0 orange, 9px 0 0 orange;
    }
    90% {
      box-shadow: 3px 0 0 orange, 9px 0 0 orange, 15px 0 0 orange;
    }
  }
</style>
```

![](/images/animation/4.gif)

```html
<style>
  .loading {
    text-align: center;
    margin-top: 100px;
  }
  .loading .dot {
    display: inline-block;
    width: 8px;
    height: 20px;
    border-radius: 5px;
    background-color: yellowgreen;

    animation: loading 0.3s infinite alternate;
  }
  .loading .dot2 {
    animation-delay: 0.2s;
  }
  .loading .dot3 {
    animation-delay: 0.4s;
  }
  .loading .dot4 {
    animation-delay: 0.6s;
  }
  @keyframes loading {
    from {
      transform: scaleY(1);
    }
    to {
      transform: scaleY(1.5);
    }
  }
</style>
```

![](/images/animation/5.gif)
