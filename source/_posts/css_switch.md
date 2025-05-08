---
title: "css原生switch按钮"
---

![](/images/css/original/css_switch.gif)

```html
<style>
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }
  body {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
  }
  label {
    /* 溢出隐藏 */
    overflow: hidden;
    position: relative;
    width: 150px;
    height: 76px;
    border-radius: 38px;
    background-color: #e9e9e9;
  }
  label input {
    /* 隐藏默认input输入框 */
    visibility: hidden;
    cursor: pointer;
  }
  /* 球体样式 */
  label input::after {
    visibility: visible;
    content: "";
    position: absolute;
    top: 5px;
    left: 5px;
    width: 66px;
    height: 66px;
    border-radius: 50%;
    background-color: #fff;
    transition: all 0.2s ease;
  }
  /* 切换动效 - 背景 */
  label input:checked::before {
    background-color: #65c456;
  }

  /* 球体背景色 */
  label input::before {
    visibility: visible;
    content: "";
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    transition: all 0.2s ease;
  }
  /* 切换动效 - 位置*/
  label input:checked::after {
    transform: translateX(74px);
  }
</style>

<body>
  <label for="switch">
    <input type="checkbox" name="switch" id="switch" />
  </label>
</body>
```
