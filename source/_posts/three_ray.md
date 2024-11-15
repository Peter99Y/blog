---
title: "three_ray & pass"
---

```typescript index.vue
<template>
  <div ref="containerRef" style="width: 100%; height: 100vh"></div>
</template>

<script lang="ts" setup>
import * as THREE from "three";
import { onMounted, ref, onBeforeUnmount } from "vue";
import { OrbitControls } from "three/addons/controls/OrbitControls.js";

import model from "./ray/ray1.ts";
// import { model, mesh1, mesh2, mesh3 } from "./ray/ray2.ts";
// import { model, sprite } from "./sprite/sprite1";

let width = 0;
let height = 0;
let containerRef = ref();

const scene = new THREE.Scene(); // 场景
let camera: THREE.PerspectiveCamera; // 相机
let renderer: THREE.WebGLRenderer; // 渲染器

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
  // addListener();
};

const addToScene = () => {
  scene.add(model);

  new OrbitControls(camera, renderer.domElement);

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

// 渲染循环
const setRender = () => {
  renderer.render(scene, camera);
  requestAnimationFrame(setRender);
};

// 画布跟随窗口变化
const setResize = () => {
  window.addEventListener("resize", () => {
    // click每次点击时，需要重新获取页面宽度；
    width = window.innerWidth;
    height = window.innerHeight;

    renderer.setSize(width, height);
    camera.aspect = width / height;
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

```typescript ray1.ts
import * as THREE from "three";

// 定义 类型化数组创建顶点数据
const vertices = new Float32Array([
  50,
  25,
  0, //顶点1坐标

  50,
  -25,
  25, //顶点2坐标

  50,
  -25,
  -25, //顶点3坐标
]);

// 创建 属性缓冲区对象；1参传入顶点数据，2参表示3个数据描述一个顶点
const attribute = new THREE.BufferAttribute(vertices, 3);
const geometry = new THREE.BufferGeometry(); // 创建 几何体对象
geometry.attributes.position = attribute; // 设置几何体顶点位置属性

const material = new THREE.MeshLambertMaterial({ color: 0x00ffff });
const mesh = new THREE.Mesh(geometry, material);
mesh.position.set(25, 0, 0);

/**
 * 射线
 * 1参 origin 三维向量，射线的起始点，默认是原点；
 * 2参 direction 三维向量，射线方向；向量长度必须为1，也就是单位向量；
 *
 * 单位向量，如不是需要.nomalize()使其标准化
 *
 * 例如：
 * ray.direction = new THREE.Vector3(1, 0, 0) 射线沿x正方向
 * ray.direction = new THREE.Vector3(0, 1, 0) 射线沿y正方向
 * ray.direction = new THREE.Vector3(1, 1, 0).normalize();
 */
const ray = new THREE.Ray();
ray.direction = new THREE.Vector3(-1, 0, 0);

// 三角形三个点坐标
const p1 = new THREE.Vector3(50, 25, 0);
const p2 = new THREE.Vector3(50, -25, 25);
const p3 = new THREE.Vector3(50, -25, -25);

// 捕获 射线和三角形的交叉点坐标
const point = new THREE.Vector3(); //用来记录射线和三角形的交叉点

/**
 * intersectTriangle
 * 1,2,3参数，组成三角形的三个坐标
 * 4参，几何体 默认逆时针 正面显示，射线如果与三角形(前3个参数)相交的话，true 从背面相交不算，只有从正面相交算
 */
const result = ray.intersectTriangle(p1, p2, p3, false, point);
console.log("是否相交", result);

export default mesh;
```

---

### Ray 射线

```typescript index.vue
import { model, mesh1, mesh2, mesh3 } from "./ray/ray2.ts";

const addListener = () => {
  /**
  坐标转化公式，屏幕坐标转标准设备坐标

  div.addEventListener('click',function(event){
      // 如果canvas全屏展示时，x和y轴像素宽度也可通过 const px = event.clientX;
      const px = event.offsetX;
      const py = event.offsetY;

      //屏幕坐标px、py转标准设备坐标x、y
      //width、height表示canvas画布宽高度
      const x = (px / width) * 2 - 1;
      const y = -(py / height) * 2 + 1;
  })
 */

  renderer.domElement.addEventListener("click", function (event) {
    const px = event.offsetX;
    const py = event.offsetY;

    // 屏幕坐标px、py转WebGL标准设备坐标x、y
    // width、height表示canvas画布宽高度
    const x = (px / width) * 2 - 1;
    const y = -(py / height) * 2 + 1;

    // 创建一个射线投射器
    const raycaster = new THREE.Raycaster();

    // setFromCamera()计算射线投射器`Raycaster`的 射线属性.ray
    // 形象点说就是在 从摄像头角度 到 点击位置 创建一条射线，射线穿过的模型代表选中
    raycaster.setFromCamera(new THREE.Vector2(x, y), camera);

    // intersectObjects  从网格模型对象数组中，捕捉有交叉点的模型对象
    const intersects = raycaster.intersectObjects([mesh1, mesh2, mesh3]);
    if (intersects.length > 0) {
      intersects[0].object.material.color.set(0xff0000);
    }

    // 触发精灵模型自定义事件
    // const intersects = raycaster.intersectObjects([sprite]);
    // if (intersects.length > 0) {
    //   intersects[0].object.change();
    // }
  });
};
```

```typescript ray2.ts
import * as THREE from "three";

const geometry = new THREE.SphereGeometry(20);
const material = new THREE.MeshLambertMaterial({
  color: 0x009999,
});

const material2 = material.clone();
const material3 = material.clone();

const mesh1 = new THREE.Mesh(geometry, material);
const mesh2 = mesh1.clone();
mesh2.material = material2;
mesh2.position.y = 100;

const mesh3 = mesh1.clone();
mesh3.material = material3;
mesh3.position.x = 100;

const model = new THREE.Group();
model.add(mesh1, mesh2, mesh3);
model.updateMatrixWorld(true);

const raycaster = new THREE.Raycaster();
console.log("射线属性", raycaster.ray);

// 设置射线起点
raycaster.ray.origin = new THREE.Vector3(-100, 0, 0);

// 设置射线方向射线方向，沿着x轴
raycaster.ray.direction = new THREE.Vector3(1, 0, 0);

// 射线发射拾取模型对象
const intersects = raycaster.intersectObjects([mesh1, mesh2, mesh3]);
// console.log("射线器返回的对象", intersects);

// intersects.length大于0说明，说明捕获了与射线相交的模型；射线先射中哪个模型，哪个模型就在数组前面；
if (intersects.length > 0) {
  //   let firstMesh = intersects[0].object;
  //   firstMesh.material.color.set(0xff0000);
  console.log("第一个模型交叉点坐标", intersects[0].point); // {X: -20, Y: 0, Z: 0}
  console.log("第二个模型交叉点坐标", intersects[1].point); // {X: 80, Y: 0, Z: 0}

  console.log("射线原点 到 交叉点的距离", intersects[0].distance); // 80
  console.log("射线原点 到 交叉点的距离", intersects[1].distance); // 180 （射线-100 + 80(100-20))
}

export { model, mesh1, mesh2, mesh3 };
```

---

### RenderPass 通道

![](/images/three/ray/pass.png)

```javascript
<template>
  <div ref="containerRef" style="width: 100%; height: 100vh"></div>
</template>

<script setup lang="ts">
import * as THREE from "three";
import { onBeforeUnmount, onMounted, ref } from "vue";
import { OrbitControls } from "three/addons/controls/OrbitControls.js";
import { EffectComposer } from "three/addons/postprocessing/EffectComposer.js";
import { RenderPass } from "three/addons/postprocessing/RenderPass.js";
import { OutlinePass } from "three/addons/postprocessing/OutlinePass.js";
import { GlitchPass } from "three/addons/postprocessing/GlitchPass.js";
import { GammaCorrectionShader } from "three/addons/shaders/GammaCorrectionShader.js";
import { ShaderPass } from "three/addons/postprocessing/ShaderPass.js";
import { SMAAPass } from "three/addons/postprocessing/SMAAPass.js";

let width = 0;
let height = 0;
let containerRef = ref();

let scene: THREE.Scene; // 场景
let renderer: THREE.WebGLRenderer; // 渲染器
let camera: THREE.PerspectiveCamera; // 相机
let controls: OrbitControls; // 相机控制器
let composer: EffectComposer; // 后期处理对象
let outlinePass: OutlinePass; // 描边通道

const init = () => {
  width = containerRef.value.clientWidth;
  height = containerRef.value.clientHeight;

  scene = new THREE.Scene();
  camera = new THREE.PerspectiveCamera(30, width / height, 1, 3000);
  camera.position.set(300, 300, 500); // 相机在三维坐标系中的位置

  // 设置渲染器绘制canvas大小
  renderer = new THREE.WebGLRenderer({
    antialias: true, // 防锯齿
    preserveDrawingBuffer: true, // 是否可以下载canvas图片
    alpha: false, // 场景透明度，使其与背景融合展示悬浮效果；
    logarithmicDepthBuffer: true, // 优化模型深度冲突，另外也可以调整模型之间position不要太近作优化；
  });
  renderer.setSize(width, height);
  renderer.setPixelRatio(window.devicePixelRatio); // 设置屏幕像素比，防止绘图模糊
  containerRef.value.appendChild(renderer.domElement);

  addPass();
  addToolsToScene();
};

const addPass = () => {
  // 创建后期处理对象
  composer = new EffectComposer(renderer);

  // 创建一个通道渲染器
  const renderPass = new RenderPass(scene, camera);
  composer.addPass(renderPass);

  // 1.描边高亮通道
  outlinePass = new OutlinePass(
    new THREE.Vector2(width, height),
    scene,
    camera
  );

  // 模型描边颜色，默认白色
  outlinePass.visibleEdgeColor.set(0xffff00);
  // 高亮发光描边厚度，默认1；
  outlinePass.edgeThickness = 1;
  // 高亮描边发光亮度，默认3；
  outlinePass.edgeStrength = 3;
  // 模型闪烁频率控制，默认0不闪烁；
  outlinePass.pulsePeriod = 5;

  // 创建闪屏通道
  // const glitchPass = new GlitchPass();
  // 设置glitchPass通道
  // composer.addPass(glitchPass);

  // 解决颜色偏差；创建伽马校正通道导致renderer.outputColorSpace无效的颜色偏差
  const gammaPass = new ShaderPass(GammaCorrectionShader);
  composer.addPass(gammaPass);

  // 解决抗锯齿；设置后期通道后会导致抗锯齿失效；
  const pixelRatio = renderer.getPixelRatio();
  const smaaPass = new SMAAPass(width * pixelRatio, height * pixelRatio);
  composer.addPass(smaaPass);

  addBoxLambertMesh();
};

// 添加受光照影响的正方体网格
const addBoxLambertMesh = () => {
  const sphereGeometry = new THREE.SphereGeometry(15, 30, 16);
  const shpereMaterial = new THREE.MeshBasicMaterial({ color: "#ADD8E6" }); // 不受光照影响
  let sphereMesh = new THREE.Mesh(sphereGeometry, shpereMaterial);
  sphereMesh.position.set(0, 0, 70);
  scene.add(sphereMesh);

  const boxGeometry = new THREE.BoxGeometry(30, 30, 30);
  const boxMaterial = new THREE.MeshLambertMaterial({ color: "#86a818" }); // 受光照影响
  let boxLambertMesh = new THREE.Mesh(boxGeometry, boxMaterial);
  scene.add(boxLambertMesh);

  // 2.添加需要描边高亮模型
  outlinePass.selectedObjects = [sphereMesh, boxLambertMesh];
  // 3.设置OutlinePass描边通道
  composer.addPass(outlinePass);
};

function addToolsToScene() {
  controls = new OrbitControls(camera, renderer.domElement);
  scene.add(new THREE.AxesHelper(100)); // 三维坐标辅助
  scene.add(new THREE.GridHelper(600, 25)); // 辅助网格地面，1参是size，一般比创建的几何体大；
  scene.add(new THREE.AmbientLight(0xffffff, 1)); // 环境光
}

// 循环渲染帧动画
function render() {
  // renderer.render(scene, camera);
  // 4.调用后期处理的render方法，内部已经调用 renderer.render方法；
  composer.render();
  controls.update();
  requestAnimationFrame(render);
}

onMounted(() => {
  init();
  render();
});

onBeforeUnmount(() => renderer.dispose());
</script>
```

---

### Ray + RenderPass

![](/images/three/ray/rayAndPass.png)

```javascript
<template>
  <div ref="containerRef" style="width: 100%; height: 100vh"></div>
</template>

<script lang="ts" setup>
import { onBeforeUnmount, onMounted, ref } from "vue";
import * as THREE from "three";
import { OrbitControls } from "three/addons/controls/OrbitControls.js";
import { EffectComposer } from "three/addons/postprocessing/EffectComposer.js";
import { RenderPass } from "three/addons/postprocessing/RenderPass.js";
import { OutlinePass } from "three/addons/postprocessing/OutlinePass.js";
import { ShaderPass } from "three/addons/postprocessing/ShaderPass.js";
import { GammaCorrectionShader } from "three/addons/shaders/GammaCorrectionShader.js";
import { GLTFLoader } from "three/addons/loaders/GLTFLoader.js";

let width = 0;
let height = 0;
let containerRef = ref();

const scene = new THREE.Scene();
let renderer: THREE.WebGLRenderer; // 渲染器
let camera: THREE.PerspectiveCamera; // 相机
let composer: EffectComposer; // 后期处理对象
let outlinePass: OutlinePass; // 描边通道

let storeBox: THREE.Object3D<THREE.Object3DEventMap>; // 存储罐

const init = () => {
  width = containerRef.value.clientWidth;
  height = containerRef.value.clientHeight;

  camera = new THREE.PerspectiveCamera(30, width / height, 1, 3000);
  camera.position.set(292, 223, 185);
  camera.lookAt(0, 0, 0);

  renderer = new THREE.WebGLRenderer();
  renderer.setSize(width, height);
  containerRef.value.appendChild(renderer.domElement);

  addComposer();
  addListener();
  addToolsToScene();
  loadModelToScene();
};

const loadModelToScene = () => {
  const loader = new GLTFLoader(); //创建一个GLTF加载器
  loader.load("/3D/factory.glb", function (gltf) {
    const topMesh = gltf.scene.getObjectByName("存储罐")!;

    for (let i = 0; i < topMesh.children.length; i++) {
      const parrentMesh = topMesh.children[i];
      parrentMesh.traverse(function (obj) {
        if (obj.type === "Mesh") {
          // 给所有子级添加共有父级，每次点击时某个子级时，高亮父级
          obj.ancestors = parrentMesh;
        }
      });
    }
    storeBox = topMesh;
    scene.add(gltf.scene);
  });
};

const addToolsToScene = () => {
  new OrbitControls(camera, renderer.domElement);

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

const addComposer = () => {
  // 创建后期处理对象
  composer = new EffectComposer(renderer);

  // 创建一个通道渲染器
  const renderPass = new RenderPass(scene, camera);
  composer.addPass(renderPass);

  // 解决颜色偏差；创建伽马校正通道导致renderer.outputColorSpace无效的颜色偏差
  const gammaPass = new ShaderPass(GammaCorrectionShader);
  composer.addPass(gammaPass);

  // 1.描边高亮通道
  outlinePass = new OutlinePass(
    new THREE.Vector2(width, height),
    scene,
    camera
  );
};

const addListener = () => {
  /**
    坐标转化公式
    div.addEventListener('click',function(event){
      // 如果canvas全屏展示时，x和y轴像素宽度也可通过 const px = event.clientX;
      const px = event.offsetX;
      const py = event.offsetY;

      //屏幕坐标px、py转标准设备坐标x、y
      //width、height表示canvas画布宽高度
      const x = (px / width) * 2 - 1;
      const y = -(py / height) * 2 + 1;
    })
 */

  renderer.domElement.addEventListener("click", function (event) {
    const px = event.offsetX;
    const py = event.offsetY;
    const x = (px / width) * 2 - 1;
    const y = -(py / height) * 2 + 1;

    const raycaster = new THREE.Raycaster();
    raycaster.setFromCamera(new THREE.Vector2(x, y), camera);

    const intersects = raycaster.intersectObjects(storeBox.children);
    console.log("intersects", intersects);

    if (intersects.length > 0) {
      outlinePass.selectedObjects = [intersects[0].object.ancestors]; // 添加需要描边高亮模型
      composer.addPass(outlinePass); // 设置OutlinePass描边通道
    } else {
      outlinePass.selectedObjects = []; // 点击其他区域，移除描边
    }
  });
};

// 渲染循环
function render() {
  // 6.调用后期处理的render方法，内部已经调用 renderer.render；
  // renderer.render(scene, camera);
  composer.render();
  requestAnimationFrame(render);
}

onMounted(() => {
  init();
  render();
});

onBeforeUnmount(() => renderer.dispose());
</script>
```
