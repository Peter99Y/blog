---
title: IntersectionObserver
---

##### 加载更多

```javascript
const isLoading = false;

// 创建重叠度观察器
let ob = new IntersectionObserver(
  (entries) => {
    // entries为数组，数组是调用observe方法所观察的元素数组
    for (const entry of entries) {
      if (entry.isIntersecting) {
        if (!isLoading) {
          isLoading = true;
          loadMore();
          isLoading = false;
        }
      }
    }
  },
  {
    root: null, // 观察与某个元素的交集，默认null是视口
    threshold: 0.5, // 重叠阈值，默认为0.5
  }
);

let observedDom = document.querySelector(".loading"); // 需要观察的元素
ob.observe(observedDom);

function loadMore() {
  console.log("loadMore");
}
```

##### 图片懒加载

```vue
<template>
  <div style="overflow-y: auto">
    <img v-lazy="it" width="375" height="400" v-for="it of imgArr" />
  </div>
</template>

<script setup>
// 引入所有静态文件 glob引入默认是懒加载模式，eager:true 静态加载,否则是懒加载;
let imgList = import.meta.glob("../assets/images/*.*", { eager: true });
let imgArr = Object.values(imgList).map((i) => i.default);

let vLazy = async (el, binding) => {
  // 1.先设置默认展位图片
  const defaultImg = await import("../assets/vue.svg");
  el.src = defaultImg.default;

  const observer = new IntersectionObserver((entries) => {
    if (entries[0].isIntersecting) {
      el.src = binding.value;
      observer.unobserve(el); // 取消监听元素
    }
  });
  observer.observe(el); // 开始监听某个元素
};
</script>
```

##### 元素滑动进入视图

```javascript
// vSlideAnimation.js
const DISTANCE = 100;
const DURATION = 200;
const map = new WeakMap();

const observer = new IntersectionObserver((entries) => {
  entries.forEach((entry) => {
    if (entry.isIntersecting) {
      const animation = map.get(entry.target);
      animation && animation.play();
      observer.unobserve(entry.target);
    }
  });
});

// 获取元素是否在视口内
function isInnerViewport(el) {
  // 获取元素相对于视口的距离信息对象
  const rect = el.getBoundingClientRect();
  // 元素的上边距离减去视口高度是否大于0， 在视口上方的距离大于视口高度，则元素不在视口内
  return rect.top - window.innerHeight > 0;
}

export const vSlideAnimation = {
  mounted(el) {
    if (!isInnerViewport(el)) return;

    // 给元素设置动画对象，并返回动画对象实例
    const animation = el.animate(
      [
        { transform: `translateY(${DISTANCE}px)` },
        { transform: "translateY(0)" },
      ],
      {
        duration: DURATION,
        ease: "ease",
        fill: "forwards",
      }
    );

    animation.pause();
    observer.observe(el);
    map.set(el, animation);
  },
  onMounted(el) {
    observer.disconnect(el);
  },
};
```
