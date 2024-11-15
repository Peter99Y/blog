---
title: "three_tag"
---

```typescript
<template>
  <div ref="containerRef" style="width: 100%; height: 100vh"></div>
</template>

<script lang="ts" setup>
import { onMounted, ref, onBeforeUnmount } from "vue";
import * as THREE from "three";
import { OrbitControls } from "three/addons/controls/OrbitControls.js";

import { model } from "./sprite/sprite1";
// import model from "./sprite/sprite2";

let width = 0;
let height = 0;
let containerRef = ref();

const scene = new THREE.Scene(); // 场景
let camera: THREE.PerspectiveCamera; // 相机
let renderer: THREE.WebGLRenderer; // 渲染器
let controls: OrbitControls; // 相机轨道控制器

const init = () => {
  width = containerRef.value.clientWidth;
  height = containerRef.value.clientHeight;

  camera = new THREE.PerspectiveCamera(30, width / height, 50, 3000);
  camera.position.set(292, 223, 185);
  camera.lookAt(0, 0, 0);

  renderer = new THREE.WebGLRenderer();
  renderer.setSize(width, height);
  containerRef.value.appendChild(renderer.domElement);

  addToScene();
};

const addToScene = () => {
  scene.add(model);

  // 轨道控制器
  controls = new OrbitControls(camera, renderer.domElement);

  // 辅助观察的坐标系
  const axesHelper = new THREE.AxesHelper(60);
  scene.add(axesHelper);

  // 添加一个辅助网格地面，1参是size，一般比创建的几何体大；
  const gridHelper = new THREE.GridHelper(600, 25);
  scene.add(gridHelper);

  // 平行光
  const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
  directionalLight.position.set(100, 60, 50);
  scene.add(directionalLight);

  // 环境光
  const ambient = new THREE.AmbientLight(0xffffff, 0.4);
  scene.add(ambient);
};

function setRender() {
  controls.update();
  renderer.render(scene, camera);
  requestAnimationFrame(setRender);
}

const setResize = () => {
  window.addEventListener("resize", () => {
    width = containerRef.value.clientWidth;
    height = containerRef.value.clientHeight;

    // 重置渲染器输出的canvas画布尺寸
    renderer.setSize(width, height);
    // 重置相机aspect视锥体长宽比
    camera.aspect = width / height;
    // camera相机的属性发生变化，需要更新 相机投影矩阵
    camera.updateProjectionMatrix();
  });
};

onMounted(() => {
  init();
  setRender();
  setResize();
});

onBeforeUnmount(() => renderer.dispose());
</script>
```

---

![](/images/three/tag/tag1.png)

```typescript sprite1.ts
import * as THREE from "three";

const texture = new THREE.TextureLoader().load("/image/light.png");

// 创建精灵材质对象SpriteMaterial
const spriteMaterial = new THREE.SpriteMaterial({
  //   color: 0x00ffff, //设置颜色
  map: texture,
  transparent: true, // 透明部分会透明，否则透明部分黑色展示，默认true；
});

// 创建精灵模型对象，不需要几何体geometry参数
const sprite = new THREE.Sprite(spriteMaterial);

// 精灵模型只有x和y，没有z；
sprite.scale.set(10, 10, 1);

// 使用精灵模型设置标注，根据几何体长宽高和精灵模型大小设置合适位置；
sprite.position.set(0, 105, -22);

// 自定义触发方法
sprite.change = function () {
  model.material.color.set(0xffff00);
};

const geometry = new THREE.BoxGeometry(25, 100, 50);
const material = new THREE.MeshLambertMaterial();

// 根据几何体高度移动到水平面以上
geometry.translate(0, 50, 0);
const model = new THREE.Mesh(geometry, material);
model.add(sprite);

export { model, sprite };
```

---

![](/images/three/tag/tag2.png)

```typescript sprite2.ts
import * as THREE from "three";
import { GLTFLoader } from "three/addons/loaders/GLTFLoader.js";

const model = new THREE.Group();

const loader = new GLTFLoader();
loader.load("/3D/factory.glb", function (gltf) {
  model.add(gltf.scene);
});

const texture = new THREE.TextureLoader().load("/image/light.png");
const spriteMaterial = new THREE.SpriteMaterial({
  map: texture,
  transparent: true,
});

// const sprite = new THREE.Sprite(spriteMaterial);
// sprite.position.set(0, 40, 0);
// sprite.scale.set(10, 10, 1);
// model.add(sprite);

const group = new THREE.Group();
model.add(group);

for (let i = 0; i < 800; i++) {
  // 精灵模型共享材质
  const sprite = new THREE.Sprite(spriteMaterial);
  group.add(sprite);
  sprite.scale.set(1, 1, 1);

  // 设置精灵模型位置，在长方体空间上上随机分布；random会取0-1随机取值，-0.5就是区间在-0.5-0.5之间；
  const x = 800 * (Math.random() - 0.5);
  const y = 400 * Math.random();
  const z = 800 * (Math.random() - 0.5);
  sprite.position.set(x, y, z);
}

const clock = new THREE.Clock();
function loop() {
  // loop()每次执行都会更新雨滴的位置，进而产生动画效果

  // 获取loop两次执行时间间隔
  const t = clock.getDelta();

  group.children.forEach((sprite) => {
    // 雨滴的y坐标每次减1
    sprite.position.y -= t * 60;

    // 如果雨滴落到地面，重置y，从新下落
    if (sprite.position.y < 0) {
      sprite.position.y = 400;
    }
  });
  requestAnimationFrame(loop);
}
loop();

export default model;
```

---

![](/images/three/tag/tag3.png)

```typescript
<template>
  <div ref="containerRef" class="model-container">
    <div ref="tagRef">tag</div>
  </div>
</template>

<script setup lang="ts">
import * as THREE from "three";
import { onBeforeUnmount, onMounted, ref } from "vue";
import { OrbitControls } from "three/addons/controls/OrbitControls.js";
import {
  CSS2DRenderer,
  CSS2DObject,
} from "three/addons/renderers/CSS2DRenderer.js";

let width = 0;
let height = 0;
let containerRef = ref();
let tagRef = ref();

let scene: THREE.Scene; // 场景
let renderer: THREE.WebGLRenderer; // 渲染器
let camera: THREE.PerspectiveCamera; // 相机
let controls: OrbitControls; // 相机控制器
let css2Renderer: CSS2DRenderer;

let boxLambertMesh: THREE.Mesh<
  THREE.BoxGeometry,
  THREE.MeshLambertMaterial,
  THREE.Object3DEventMap
>;

let boxInitialPosition = new THREE.Vector3(0, 0, 0);

const init = () => {
  width = containerRef.value.clientWidth;
  height = containerRef.value.clientHeight;

  // 设置渲染器绘制canvas大小
  renderer = new THREE.WebGLRenderer({
    antialias: true, // 防锯齿
    preserveDrawingBuffer: true, // 是否可以下载canvas图片
    alpha: false, // 场景透明度，使其与背景融合展示悬浮效果；
    logarithmicDepthBuffer: true, // 优化模型深度冲突，另外也可以调整模型之间position不要太近作优化；
  });
  renderer.setSize(width, height);
  renderer.setPixelRatio(window.devicePixelRatio); // 设置屏幕像素比，防止绘图模糊
  containerRef.value.appendChild(renderer.domElement); // 将canvas插入到DOM中

  // 创建场景
  scene = new THREE.Scene();

  // 创建相机
  camera = new THREE.PerspectiveCamera(30, width / height, 1, 3000);
  camera.position.set(300, 300, 500); // 相机在三维坐标系中的位置

  // 设置控制器
  controls = new OrbitControls(camera, renderer.domElement);

  scene.add(new THREE.AxesHelper(100)); // 三维坐标辅助
  scene.add(new THREE.GridHelper(600, 25)); // 辅助网格地面，1参是size，一般比创建的几何体大；
  scene.add(new THREE.AmbientLight(0xffffff, 1)); // 环境光

  addBoxLambertMesh();
};

function addToScene() {
  // 创建一个CSS2渲染器CSS2DRenderer
  css2Renderer = new CSS2DRenderer();
  css2Renderer.render(scene, camera);
  css2Renderer.setSize(width, height);
  css2Renderer.domElement.style.position = "absolute";
  // css2Renderer.domElement.style.top = "100px";  // 注意和父级容器重合
  // css2Renderer.domElement.style.left = "100px";

  css2Renderer.domElement.style.top = "0"; // 注意和父级容器重合
  css2Renderer.domElement.style.color = "#fff";
  css2Renderer.domElement.style.pointerEvents = "none"; // 解决图层遮挡
  document.body.appendChild(css2Renderer.domElement);
}

// 添加受光照影响的正方体网格
const addBoxLambertMesh = () => {
  const meshGroup = new THREE.Group();
  const tagGroup = new THREE.Group();

  const geometry = new THREE.BoxGeometry(50, 50, 50);
  const material = new THREE.MeshLambertMaterial();
  material.color.set("#86a818");
  boxLambertMesh = new THREE.Mesh(geometry, material);
  boxLambertMesh.position.copy(boxInitialPosition);
  meshGroup.add(boxLambertMesh);
  meshGroup.position.x = -100;

  const tag = new CSS2DObject(tagRef.value); // HTML元素转化为threejs的CSS2模型对象
  // boxLambertMesh.add(tag); // 方式2：直接将tag作为子对象添加，它会受到父级位置的影响；

  let wordV3 = new THREE.Vector3(); // 方式1：设置tag对应的位置，根据模型的世界坐标位置
  boxLambertMesh.getWorldPosition(wordV3);
  tag.position.copy(wordV3);

  // 设置tag位于几何体哪个顶点上
  // let positions = geometry.attributes.position;
  // tag.position.set(positions.getX(1), positions.getY(1), positions.getZ(1));

  tagGroup.add(tag);
  scene.add(meshGroup, tagGroup);
};

// 循环渲染帧动画
function render() {
  controls.update();
  css2Renderer.render(scene, camera);
  renderer.render(scene, camera);
  requestAnimationFrame(render);
}

const setResize = () => {
  window.addEventListener("resize", () => {
    width = containerRef.value.clientWidth;
    height = containerRef.value.clientHeight;

    css2Renderer.setSize(width, height);
    // 重置渲染器输出的canvas画布尺寸
    renderer.setSize(width, height);
    // 重置相机aspect视锥体长宽比
    camera.aspect = width / height;
    // camera相机的属性发生变化，需要更新 相机投影矩阵
    camera.updateProjectionMatrix();
  });
};

onMounted(() => {
  init();
  addToScene();
  render();
  setResize();
});

onBeforeUnmount(() => renderer.dispose());
</script>

<style scoped>
.model-container {
  width: 100%;
  height: 100vh;
  /* margin-top: 100px; 注意和父级容器重合 */
  /* margin-left: 100px; */
}
</style>
```
