---
title: "css进度"
---

## 圆形波浪进度条

![](/images/css/original/wave.gif)

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

```html vue3
<template>
  <div class="wave-progress-wrapper">
    <div class="indicator" :style="progressStyle">
      <span>{{ percent }}%</span>
    </div>
  </div>
</template>

<script setup>
  const props = defineProps({
    percent: Number,
  });

  const progressStyle = computed(() => {
    return {
      "--top": `${100 - props.percent}%`,
    };
  });
</script>

<style lang="scss" scoped>
  .wave-progress-wrapper {
    position: relative;
    z-index: 1;
    padding: 3px;
    border-radius: 50%;
    background: linear-gradient(to top, #bbe5fc, #c7c4fc);

    .indicator {
      position: relative;
      z-index: 3;

      --circle-length: 31px;
      --wave-length: calc(var(--circle-length) * 2);
      --wave-radius: calc(var(--wave-length) / 2 - 10px);

      width: var(--circle-length);
      height: var(--circle-length);
      border-radius: 50%;
      color: #fff;
      font-size: 14px;
      font-weight: bold;
      position: relative;
      overflow: hidden;
      display: flex;
      align-items: center;
      justify-content: center;

      &::after {
        content: "";
        display: block;
        width: var(--wave-length);
        height: var(--wave-length);
        border-radius: var(--wave-radius);
        background: #4879ff;

        position: absolute;
        z-index: -1;
        top: var(--top);
        animation: spin 7s linear infinite;
      }
    }

    @keyframes spin {
      0% {
        transform: rotateZ(0deg);
      }
      100% {
        transform: rotateZ(360deg);
      }
    }
  }
</style>
```

## 电池进度条

![](/images/css/original/css_progress.png)

```html
<style>
  .battery {
    display: flex;
    align-items: center;
    gap: 2px;
  }

  /* 电池主体 */
  .battery-body {
    width: 100px;
    height: 40px;
    border: 3px solid #333;
    border-radius: 5px;
    position: relative;
    overflow: hidden;
  }

  /* 电量填充部分 */
  .battery-level {
    position: absolute;
    top: 0;
    left: 0;
    bottom: 0;
    width: var(--level); /* 通过 CSS 变量控制电量 */
    background-color: #4caf50; /* 绿色表示电量 */
    transition: width 0.3s ease;
  }

  /* 电池正极（小凸起） */
  .battery-tip {
    width: 6px;
    height: 15px;
    background-color: #333;
    border-radius: 0 3px 3px 0;
  }
</style>

<body>
  <div class="battery">
    <div class="battery-body">
      <div class="battery-level" style="--level: 60%"></div>
    </div>
    <div class="battery-tip"></div>
  </div>
</body>
```

## 分段状态进度条

```html progress.html
<style>
  .progress {
    width: 300px;
    height: 8px; /* 稍微加高一点 */
    border: 2px solid #333;
    border-radius: 5px; /* 圆角更大些 */
    background-color: #f5f5f5; /* 添加背景色 */
    display: grid;
    grid-template-columns: repeat(5, 1fr); /* 更简洁的写法 */
    gap: 2px; /* 间隙更小 */
    padding: 2px; /* 内边距使边框更美观 */
    overflow: hidden; /* 防止子元素溢出圆角 */
  }

  .progress .block {
    position: relative;
    background-color: rgba(233, 233, 233, 0.7);
    border-radius: 2px; /* 添加小圆角 */
  }

  .progress .block .level {
    position: absolute;
    top: 0;
    left: 0;
    bottom: 0;
    width: var(--level, 0%);
    background-color: #4caf50;

    transition: width 0.1s ease;
  }

  .progress .block .level.low {
    background-color: #ff5722;
  }
  .progress .block .level.medium {
    background-color: #ffc107;
  }
</style>

<body>
  <div class="progress" id="myProgress">
    <!-- 5个块，JS会动态控制 -->
    <div class="block"><div class="level"></div></div>
    <div class="block"><div class="level"></div></div>
    <div class="block"><div class="level"></div></div>
    <div class="block"><div class="level"></div></div>
    <div class="block"><div class="level"></div></div>
  </div>

  <div style="margin-top: 20px">
    <button onclick="setProgress(20)">20%</button>
    <button onclick="setProgress(50)">50%</button>
    <button onclick="setProgress(80)">80%</button>
    <button onclick="setProgress(100)">100%</button>
  </div>

  <script>
    const progressBar = document.getElementById("myProgress");
    const blocks = progressBar.querySelectorAll(".level");

    function setProgress(percent) {
      const totalBlocks = blocks.length; // 总的块数
      const blockValue = 100 / totalBlocks; // 每个块占比20%

      // 先全部块重置为0%
      blocks.forEach((block) => {
        block.style.setProperty("--level", "0%");
      });

      // 计算完全填充的block数量 (如70%，则全部填充块是3个)
      const fullBlocks = Math.floor(percent / blockValue);

      // 填充完整的block
      for (let i = 0; i < fullBlocks; i++) {
        if (i < totalBlocks) {
          blocks[i].style.setProperty("--level", "100%");
        }
      }

      // 处理最后一个部分填充的block (如70%， 前3个填充满，处理第4个block填充宽度)
      // 一.70 % 20 取余数10;  二. 10除20 = 0.5;  三. 0.5*100 = 50% (最终得到第四个填充宽度50%)；
      const partialFill = ((percent % blockValue) / blockValue) * 100;

      // 完整填充的数量 小于 总数 并且 最后一块填充比例大于0，设置第四个block宽度；
      if (fullBlocks < totalBlocks && partialFill > 0) {
        blocks[fullBlocks].style.setProperty("--level", `${partialFill}%`);
      }

      // 设置颜色
      blocks.forEach((block) => {
        block.classList.remove("low", "medium");
        if (percent <= 30) {
          block.classList.add("low");
        } else if (percent <= 80) {
          block.classList.add("medium");
        }
      });
    }

    // 初始化
    setProgress(40);
  </script>
</body>
```

```vue progress.vue
<template>
  <div class="progress-wrapper">
    <div class="block" v-for="(block, index) in blockList" :key="index">
      <div
        class="level"
        :class="levelClass"
        :style="{ '--bg-color': bgColor, '--level': block.level }"
      ></div>
    </div>
  </div>
</template>

<script setup>
const props = defineProps({
  percent: {
    type: [Number, String],
    default: 0,
  },
  bgColor: {
    type: String,
    default: "#4caf50",
  },
});

const totalBlocks = 5; // 默认5个block，与css中定义的block数量一致；
const blockValue = 100 / totalBlocks; // 每个block比例为20

const blockList = computed(() => {
  // [{ level: "0%"},...], 初始化对象数组，默认填充比例为0%；
  const newBlocks = Array(totalBlocks).fill({ level: "0%" });

  // 获取完整填充的块数量 (如70%，则完整填充块位为3个)
  const fullBlocks = Math.floor(props.percent / blockValue);

  // 处理最后一个部分填充的block (如70%，前3个填充满，处理第4个block填充宽度比例)
  // 一.70 % 20 取余10;  二. 10除20 = 0.5;  三. 0.5*100 = 50% (最终得到第四个块的填充比例为50%)；
  const partialFill = ((props.percent % blockValue) / blockValue) * 100;

  // 先填充前3个完整的块
  for (let i = 0; i < fullBlocks; i++) {
    if (i < totalBlocks) {
      newBlocks[i] = { level: "100%" };
    }
  }

  // 最后设置第四个填充块的比例
  if (fullBlocks < totalBlocks && partialFill > 0) {
    newBlocks[fullBlocks] = { level: `${partialFill}%` };
  }

  return newBlocks;
});

const levelClass = computed(() => {
  if (props.percent <= 20) return "low";
  if (props.percent <= 80) return "medium";
  return "";
});
</script>

<style scoped lang="scss">
.progress-wrapper {
  width: 180px;
  height: 15px;
  border-radius: 3px;
  background-color: #fff;

  display: grid;
  grid-template-columns: repeat(5, 1fr);
  gap: 3px;
  padding: 2px;
  overflow: hidden;

  .block {
    position: relative;
    border-radius: 3px;
    background-color: rgba(233, 233, 233, 0.7);

    .level {
      position: absolute;
      top: 0;
      left: 0;
      bottom: 0;
      width: var(--level);
      background-color: var(--bg-color);
      border-radius: inherit;
      transition: width 0.1s ease;
    }

    .level.low {
      background-color: #ff5722;
    }
    .level.medium {
      background-color: #ffc107;
    }
  }
}
</style>
```
