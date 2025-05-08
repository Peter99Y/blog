---
title: "js原生输入密码框"
---

![](/images/css/original/css_password.gif)

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
  h2 {
    color: #666;
    font-weight: normal;
    font-size: 14px;
    margin-bottom: 10px;
  }
  .wrapper input {
    width: 30px;
    height: 30px;
    text-align: center;
    border: 1px solid #ccc;
    margin-right: 5px;
  }
</style>

<body>
  <div class="wrapper">
    <h2>请输入密码</h2>

    <input type="text" maxlength="1" />
    <input type="text" maxlength="1" />
    <input type="text" maxlength="1" />
    <input type="text" maxlength="1" />
  </div>

  <script>
    const inputs = document.querySelectorAll(".wrapper input");

    inputs.forEach((input, index) => {
      // 绑定输入框输入事件
      input.addEventListener("input", function () {
        const value = this.value; // 当前input输入的值
        console.log("input", this.value);

        if (this.type === "text") {
          setTimeout(() => {
            this.type = "password";
          }, 300);
        }

        // Backspace按钮删除后也会自动触发input事件
        if (value.length === 0) {
          this.type = "text";
        }

        if (value.length === 1) {
          const nextIndex = index + 1;
          if (nextIndex < inputs.length) {
            const nextInput = inputs[nextIndex];
            nextInput.focus();
          }
        }
      });

      // 监听键盘抬起事件
      input.addEventListener("keydown", function (e) {
        if (e.key === "Backspace") {
          // 判断当前输入框的值是否为空
          if (this.value.length === 0) {
            const prevIndex = index - 1;

            if (prevIndex >= 0) {
              const prevInput = inputs[prevIndex];
              prevInput.focus();
              prevInput.select();
            }
          }
        }
      });
    });
  </script>
</body>
```
