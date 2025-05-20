---
title: "css原生手电筒灯光"
---

![](/images/css/original/css_light.png)

```html
<style>
  * {
    padding: 0;
    margin: 0;
    box-sizing: border-box;
  }
  :root {
    --x: 0px;
    --y: 0px;
  }
  section {
    position: relative;
    min-height: 100vh;
    background-color: #000;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  section h2 {
    color: #fff;
    font-size: 15em;
    cursor: default;
  }
  section .light {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    pointer-events: none;
    background: radial-gradient(
      circle at var(--x) var(--y),
      transparent 10%,
      rgba(0, 0, 0, 0.95) 20%
    );
  }
</style>

<body>
  <section>
    <h2>Light</h2>
    <div class="light"></div>
  </section>

  <script>
    const doc = document.documentElement;
    doc.addEventListener("mousemove", (e) => {
      doc.style.setProperty("--x", e.clientX + "px");
      doc.style.setProperty("--y", e.clientY + "px");
    });
  </script>
</body>
```
