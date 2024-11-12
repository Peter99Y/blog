---
title: three_texture
---

```typescript index.vue
<template>
  <div ref="containerRef" style="width: 100%; height: 100vh"></div>
</template>

<script lang="ts" setup>
import * as THREE from "three";
import { ref, onMounted, onBeforeUnmount } from "vue";
import { OrbitControls } from "three/addons/controls/OrbitControls.js";

import model from "./texture/texture.ts";

let width = 0;
let height = 0;
let containerRef = ref();
const scene = new THREE.Scene(); // 场景
let camera: THREE.PerspectiveCamera; // 相机
let renderer: THREE.WebGLRenderer; // 渲染器

const init = () => {
  width = containerRef.value.clientWidth;
  height = containerRef.value.clientHeight;

  camera = new THREE.PerspectiveCamera(30, width / height, 1, 3000);
  camera.position.set(292, 223, 185);
  camera.lookAt(0, 0, 0);

  renderer = new THREE.WebGLRenderer();
  renderer.setSize(width, height);
  containerRef.value.appendChild(renderer.domElement);

  // gltf文件默认导出的是THREE.sRGBEncoding编码格式， renderer默认是THREE.LinearEncoding
  // 重置成一致的格式，解决加载gltf格式模型颜色偏差问题
  renderer.outputColorSpace = THREE.SRGBColorSpace;

  // 将点模型对象添加到场景中
  scene.add(model);
  addToScene();
};

const addToScene = () => {
  //辅助观察的坐标系
  const axesHelper = new THREE.AxesHelper(500);
  scene.add(axesHelper);

  // 辅助网格，1参是size，一般比创建的几何体大；
  const gridHelper = new THREE.GridHelper(600, 25);
  scene.add(gridHelper);

  // 平行光
  const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
  directionalLight.position.set(100, 60, 50);
  scene.add(directionalLight);

  // 环境光
  const ambient = new THREE.AmbientLight(0xffffff, 0.4);
  scene.add(ambient);

  new OrbitControls(camera, renderer.domElement);
};

const setRender = () => {
  renderer.render(scene, camera);
  requestAnimationFrame(setRender);
};

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

### TextureLoader 纹理加载器

### geometry.attributes.uv 纹理 uv (水平垂直) 坐标

### material.map 纹理贴图

### colorSpace 编码色差

![](/images/three/texture/texture1.png)

```typescript texture.ts
import * as THREE from "three";

const geometry = new THREE.BufferGeometry();

//  定义 类型化数组创建顶点数据；使用顶点索引时，需将相邻顶点移除
const vertices = new Float32Array([
  0,
  0,
  0, // 顶点1，下标0

  50,
  0,
  0,

  50,
  50,
  0,

  0,
  50,
  0,
]);

// 创建 属性缓冲区对象；1参传入顶点数据，2参表示1数据描述一个顶点
const attribute = new THREE.BufferAttribute(vertices, 3);
// 设置几何体顶点位置属性
geometry.attributes.position = attribute;

// 顶点索引数据
const indexes = new Uint16Array([0, 1, 2, 2, 3, 0]);
// 设置几何体顶点索引
geometry.index = new THREE.BufferAttribute(indexes, 1);

// 设置纹理坐标，0~1之间按索取贴图位置百分比来定义；
const uvs = new Float32Array([
  0,
  0, // 图片左下角

  1,
  0, // 图片右下角

  1,
  1, // 图片右上角

  0,
  1, // 图片左上角
]);

// 2个为一组,表示一个顶点的纹理坐标
geometry.attributes.uv = new THREE.BufferAttribute(uvs, 2);

// 创建 纹理贴图加载器 TextureLoader
const texture = new THREE.TextureLoader().load(
  "../../../public/image/earth.jpg"
);
// 材质默认是THREE.LinearEncoding，而导入的gltf模型和图片一般都是THREE.sRGBEncoding；
// 重置材质encoding，解决色差问题；
texture.colorSpace = THREE.SRGBColorSpace;

const material = new THREE.MeshBasicMaterial();
material.map = texture;
const mesh = new THREE.Mesh(geometry, material);

// CircleGeometry 是按照 圆形半径 采用 纹理贴图的中心部分比例，多余会被舍弃；
const circleGeometry = new THREE.CircleGeometry(30);

const texture2 = new THREE.TextureLoader().load("/image/panda.jpg");
texture2.colorSpace = THREE.SRGBColorSpace;

const material2 = new THREE.MeshBasicMaterial();
material2.map = texture2;

const circle = new THREE.Mesh(circleGeometry, material2);
circle.position.z = 50;

const group = new THREE.Group();
group.add(mesh);
group.add(circle);

export default group;
```

---

### 纹理透明度

![](/images/three/texture/texture2.png)

```typescript texture.ts
import * as THREE from "three";

const geometry = new THREE.PlaneGeometry(50, 50);
const texLoader = new THREE.TextureLoader();
const texture = texLoader.load("../../../public/image/arrow.png");

const material = new THREE.MeshLambertMaterial({
  map: texture,
  transparent: true, // 开启透明，这样png贴图的透明部分不显示
});

const mesh = new THREE.Mesh(geometry, material);

// 旋转矩形平面
mesh.rotateX(-Math.PI / 2);

export default mesh;
```

---

### 纹理阵列

![](/images/three/texture/texture3.png)

```typescript texture.ts
import * as THREE from "three";

const geometry = new THREE.PlaneGeometry(500, 500);
const texLoader = new THREE.TextureLoader();

const texture = texLoader.load("../../../public/image/tile.jpg");

// 设置阵列水平和垂直方向 和 阵列模式
texture.wrapS = THREE.RepeatWrapping;
texture.wrapT = THREE.RepeatWrapping;

// uv纹理坐标两个方向重复数量，在整个几何体大小内被重复；
texture.repeat.set(3, 3); //注意选择合适的阵列数量

const material = new THREE.MeshLambertMaterial({
  map: texture,
});

const mesh = new THREE.Mesh(geometry, material);

// 旋转矩形平面
mesh.rotateX(-Math.PI / 2);

export default mesh;
```

---

### 纹理动画

### 纹理重复 THREE.RepeatWrapping / ClampToEdgeWrapping

![](/images/three/texture/texture4.png)

```typescript texture.ts
import * as THREE from "three";

// 一个矩形平面几何体用来表示传送带
const geometry = new THREE.PlaneGeometry(200, 20);
const texLoader = new THREE.TextureLoader();
const texture = texLoader.load("/image/zebra.jpg");

const material = new THREE.MeshLambertMaterial();
material.map = texture;

const model = new THREE.Mesh(geometry, material);
model.rotateX(-Math.PI / 2);

/**
 *  texture.wrapS 等于 纹理U坐标水平方向，texture.wrapT 等于 纹理V坐标垂直方向

 *  纹理映射模式 (包裹模式/重复方式)
    RepeatWrapping：空白部分会再次用贴图拼接重复；
    ClampToEdgeWrapping：如纹理偏移量后，贴图不够的部分空白展示；
 */
texture.wrapS = THREE.RepeatWrapping; // 对应offste.x偏移
// texture.wrapS = THREE.ClampToEdgeWrapping;

export { model, texture };
```

```typescript index.vue
<script lang="ts" setup>
import {model, texture} from "./texture/texture.ts";

// ...

const setRender = () => {
  // 设置纹理偏移量 和 纹理重复模式，实现动画效果
  texture.offset.x += 0.001;
  renderer.render(scene, camera);
  requestAnimationFrame(setRender);
};
</script>
```

---

### 纹理翻转

![](/images/three/texture/texture5.png)

```typescript
import * as THREE from "three";

// 加载库GLTFLoader加载gltf模型
import { GLTFLoader } from "three/addons/loaders/GLTFLoader.js";

// 声明一个组对象，用来添加加载成功的三维场景
const model = new THREE.Group();

const loader = new GLTFLoader();

const texLoader = new THREE.TextureLoader();
const phoneTexture = texLoader.load("/image/phone_map.png");
phoneTexture.colorSpace = THREE.SRGBColorSpace;

loader.load("/3D/phone.glb", function (gltf) {
  const mesh = gltf.scene.children[0];

  // 纹理对象 texture.flipY 是否翻转纹理贴图，默认true;
  // gltf里面Mesh颜色贴图的.flipY值保持一致；
  phoneTexture.flipY = mesh.material.map.flipY;

  mesh.material.map = phoneTexture; // 颜色贴图
  mesh.position.set(0, 16, 100);
  mesh.scale.set(0.5, 0.5, 0.5);
  model.add(gltf.scene);
});

loader.load("/3D/community.glb", function (gltf) {
  // traverse递归遍历所有模型节点批量修改材质
  gltf.scene.traverse(function (obj) {
    if (obj.type === "Mesh" && obj.name) {
      // 判断是否是网格模型，重新设置材质
      obj.material = new THREE.MeshLambertMaterial({
        color: 0xffffff,
      });

      if (obj.material.map) {
        console.log("colorSpace", obj.material.map.colorSpace);
      }
    }
  });

  const firstFlat = gltf.scene.getObjectByName("1号楼")!;
  firstFlat.material.color.set(0xffff00);

  const arr = gltf.scene.getObjectByName("洋房")!; // 获得所有'洋房'房子的父对象
  arr.children.forEach(function (mesh) {
    mesh.material.color.set("#666"); // arr.children都是Mesh，设置Mesh颜色
  });

  // 1.用代码方式解决mesh共享材质问题；2.在三维建模软件中设置独享材质；
  // gltf.scene.getObjectByName("小区房子").traverse(function (obj) {
  //   if (obj.isMesh) {
  //     // clone 返回一个新材质对象，和原来一样，重新赋值给material属性
  //     obj.material = obj.material.clone();
  //   }
  // });

  model.add(gltf.scene); // 三维场景添加到model组对象中
});

export default model;
```

### 其他纹理属性

![](/images/three/texture/texture6.png)

```typescript
import * as THREE from "three";
import { GLTFLoader } from "three/addons/loaders/GLTFLoader.js";
import { GUI } from "three/addons/libs/lil-gui.module.min.js";

const gui = new GUI();
const loader = new GLTFLoader(); //创建一个GLTF加载器
const model = new THREE.Group(); //声明一个组对象，用来添加加载成功的三维场景

// 加载环境贴图
// 加载周围环境6个方向贴图
// 上下左右前后6张贴图构成一个立方体空间
// 'px.jpg', 'nx.jpg'：x轴正方向、负方向贴图  p:正positive  n:负negative
// 'py.jpg', 'ny.jpg'：y轴贴图
// 'pz.jpg', 'nz.jpg'：z轴贴图

// CubeTexture表示立方体纹理对象
const textureCube = new THREE.CubeTextureLoader()
  .setPath("../../assets/envMap/")
  .load(["px.jpg", "nx.jpg", "py.jpg", "ny.jpg", "pz.jpg", "nz.jpg"]);
textureCube.colorSpace = THREE.SRGBColorSpace;

loader.load("/3D/metal.glb", function (gltf) {
  gltf.scene.traverse(function (obj: any) {
    if (obj.type === "Mesh") {
      // 重新设置材质的金属度和粗糙度属性
      obj.material.metalness = 1.0; // 金属度；与金属相似度
      obj.material.roughness = 0.3; // 表面粗糙度; 0光滑得像镜面反射，1完全漫反射;

      // 在没有环境光源情况下，环境贴图提供了物体周围环境反射的光线，也就不会完全看不见有了光线
      obj.material.envMap = textureCube; // 遍历方式，设置每个模型环境贴图；另外一种scene.environment;
      obj.material.envMapIntensity = 1.0; // 环境贴图对模型表面影响程度；默认值1，0相当于没有环境贴图
    }
  });
  model.add(gltf.scene);
});

loader.load("/3D/car.glb", function (gltf) {
  model.add(gltf.scene);
  gltf.scene.scale.set(0.2, 0.2, 0.2);
  gltf.scene.position.z = 80;

  // 车外壳包含多个Mesh，获取其中一个
  const mesh = gltf.scene.getObjectByName("外壳01");
  mesh.material = new THREE.MeshPhysicalMaterial({
    clearcoat: 1, //清漆层
    color: mesh.material.color, //默认颜色
    metalness: 0.9, //车外壳金属度
    roughness: 0.5, //车外壳粗糙度
    clearcoatRoughness: 0.01, //清漆层粗糙度
    envMap: textureCube, //环境贴图
    envMapIntensity: 2.5, //环境贴图对Mesh表面影响程度
  });
  setShellGui(mesh);

  const mesh2 = gltf.scene.getObjectByName("玻璃01");
  mesh2.material = new THREE.MeshPhysicalMaterial({
    metalness: 0.0, // 玻璃非金属
    roughness: 0.0, // 玻璃表面光滑
    envMap: textureCube, // 环境贴图
    envMapIntensity: 1.0, // 环境贴图对Mesh表面影响程度
    transmission: 1.0, // 透光率，transmission替代opacity
    ior: 1.5, // 折射率
  });
  setGlassGui(mesh2);
});

const setShellGui = (mesh: any) => {
  const matFolder = gui.addFolder("车壳");
  matFolder.add(mesh.material, "metalness", 0, 1);
  matFolder.add(mesh.material, "roughness", 0, 1);
  matFolder.add(mesh.material, "clearcoat", 0, 1);
  matFolder.add(mesh.material, "clearcoatRoughness", 0, 1);
  matFolder.add(mesh.material, "envMapIntensity", 0, 10);

  const obj = {
    color: mesh.material.color.getHex(), // 获取材质默认颜色
  };
  matFolder.addColor(obj, "color").onChange(function (value) {
    mesh.material.color.set(value);
  });
};

const setGlassGui = (mesh: any) => {
  const matFolder2 = gui.addFolder("前挡风玻璃");
  matFolder2.add(mesh.material, "metalness", 0, 1);
  matFolder2.add(mesh.material, "roughness", 0, 1);
  matFolder2.add(mesh.material, "transmission", 0, 1);
  matFolder2.add(mesh.material, "ior", 1, 2.333);
  matFolder2.add(mesh.material, "envMapIntensity", 0, 10);
};

export default model;
```
