---
title: "波浪进度"
---

[参考文章](https://cloud.tencent.com/developer/article/1800814)

```HTML
<html class="full"></html>
<head>
</head>
<style>
  body {
    background: #000;
  }
  .indicator {
    width: 100px;
    height: 100px;
    margin: 200px auto;
    border-radius: 50%;
    color: #fff;
    font-size: 24px;
    border: 4px double yellowgreen;

    position: relative;
    overflow: hidden;

    display: flex;
    align-items: center;
    justify-content: center;
  }
  .indicator::after {
    content: "";
    display: block;
    width: 200px;
    height: 200px;
    border-radius: 75px;
    background: lightblue;

    position: absolute;
    z-index: -1;
    left: -50%;
    top: 100%;
    animation: spin 5s linear infinite;
  }
  @keyframes spin {
    0% {
      transform: rotateZ(0deg);
    }
    100% {
      transform: rotateZ(360deg);
    }
  }
</style>

<body>

    <div class="indicator">
        <span>50</span>
    </div>

</body>
</html>
```
