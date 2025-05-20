---
title: js原生垂直无线滚动
---

![](/images/css/original/css_scroll.png)

```html
<style>
  * {
    padding: 0;
    margin: 0;
    box-sizing: border-box;
  }
  .container {
    background-color: yellowgreen;
    padding: 20px 0;
  }
  .container::after {
    content: "";
    display: block;
    clear: both;
  }
  .title {
    float: left;
    font-size: 16px;
    font-weight: normal;
    margin-left: 20px;
    margin-top: 5px;
    border-right: 2px solid #ccc;
    padding-right: 10px;
    margin-right: 10px;
  }
  .list {
    list-style: none;
    padding: 0;
    margin: 0;
    height: 30px;
    overflow: hidden;
    margin-left: 10px;
  }
  .list li {
    height: 30px;
    line-height: 30px;
  }
</style>

<body>
  <div class="container">
    <h1 class="title">公告:</h1>
    <ul class="list">
      <li>1.Lorem ipsum dolor sit amet.</li>
      <li>2.Doloremque quia earum nam corporis.</li>
      <li>3.Iure magni sunt quidem porro!</li>
      <li>4.Temporibus tempora natus commodi maxime.</li>
    </ul>
  </div>

  <script>
    (function () {
      let list = document.querySelector(".list");
      let firstItem = list.children[0];
      let lastItem = firstItem.cloneNode(true); // 深度克隆，否则只是克隆标签元素，内容没有；
      list.appendChild(lastItem);

      let duration = 2000;
      let currentIndex = 0;
      let itemHeight = 30;
      function move() {
        let from = currentIndex * itemHeight; // 滚动前高度
        currentIndex++;
        let to = currentIndex * itemHeight; // 滚动后高度

        let totalDuration = 500;
        let d = 10;
        let times = totalDuration / d; // 变化次数
        let distance = (to - from) / times; // 每次变化距离

        // 模拟丝滑滚动
        let timer = setInterval(() => {
          from += distance;
          if (from >= to) {
            clearInterval(timer);

            if (currentIndex === list.children.length - 1) {
              from = 0;
              currentIndex = 0;
            }
          }
          list.scrollTop = from;
        }, d);
      }

      setInterval(move, duration);
    })();
  </script>
</body>
```
