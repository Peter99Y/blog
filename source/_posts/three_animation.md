---
title: "three_animation"
---

```TYPESCRIPT
<template>
  <div class="btn-wrapper">
    <button :disabled="getStarted" @click="onPlay">播放一段</button>
    <button :disabled="!getStarted" @click="onStop">结束</button>
    <button :disabled="!getStarted" @click="onPause">
      {{ isPause ? "继续" : "暂停" }}
    </button>
    <button @click="onNext">下一步</button>
    <button @click="onReset">重置</button>
  </div>

  <div ref="containerRef" style="width: 100%; height: 100vh"></div>
</template>

<script lang="ts" setup>
import { onMounted, ref, onBeforeUnmount } from "vue";
import * as THREE from "three";
import { OrbitControls } from "three/addons/controls/OrbitControls.js";
import { GUI } from "three/addons/libs/lil-gui.module.min.js";
import { GLTFLoader } from "three/addons/loaders/GLTFLoader.js";

let width = 0;
let height = 0;
let containerRef = ref();

// 场景
const scene = new THREE.Scene();
let camera: THREE.PerspectiveCamera; // 相机
let renderer: THREE.WebGLRenderer; // 渲染器
let controls: OrbitControls; // 相机轨道控制器

let clip: THREE.AnimationClip; // 关键帧轨道集
let mixer: THREE.AnimationMixer; // 动画混合器播放器
let carMixer: THREE.AnimationMixer;
let clipAction: THREE.AnimationAction;

const clock = new THREE.Clock();
const getStarted = ref(false);
const isPause = ref(false);
const duration = 4; // 动画时长


const init = () => {
  width = containerRef.value.clientWidth;
  height = containerRef.value.clientHeight;

  camera = new THREE.PerspectiveCamera(30, width / height, 1, 3000);
  camera.position.set(300, 300, 500); // 相机在三维坐标系中的位置
  camera.lookAt(0, 0, 0);

  renderer = new THREE.WebGLRenderer();
  renderer.setSize(width, height);
  renderer.outputColorSpace = THREE.SRGBColorSpace;
  containerRef.value.appendChild(renderer.domElement);
};

const addMeshToScene = () => {
  const geometry = new THREE.BoxGeometry(10, 10, 10);
  const material = new THREE.MeshLambertMaterial();
  const mesh = new THREE.Mesh(geometry, material);
  scene.add(mesh);

  addKeyFrameAnimation(mesh);
  addModelAnimation();
  addTwistingAnimation();
  addBoneAnimation();
};

const onPlay = () => {
  // 设置开始播放时间和动画时长就能设置某一动画片段；
  clipAction.time = 1; // 设置开始播放时间：从1秒时刻对应动画开始播放
  clip.duration = 3; // 设置播放结束时间：到5秒时刻对应的动画状态停止
  clipAction.paused = false;
  getStarted.value = true;
};
const onPause = () => {
  if (clipAction.paused) {
    clipAction.paused = false;
    isPause.value = false;
  } else {
    clipAction.paused = true;
    isPause.value = true;
  }
};
const onStop = () => {
  clipAction.stop();
  getStarted.value = false;
};
const onReset = () => {
  clipAction.reset();
  clip.duration = duration;
  getStarted.value = false;
};
const onNext = () => {
  clipAction.paused = true;
  clipAction.time += 1;
  if (clipAction.time >= duration) clipAction.time = 0;
};
const onFinished = (data: any) => {
  console.log("全部动作结束", data);
  getStarted.value = false;
};

const setGui = () => {
  const gui = new GUI();
  gui.domElement.style.top = "10px";
  gui.domElement.style.right = "10px";
  gui.domElement.style.width = "300px";
  gui
    .add(clipAction, "timeScale", 0, 2)
    .name("倍数")
    .step(0.2)
    .onChange((val: number) => {
      clipAction.timeScale = val;
    });

  // 0 - 4, 结束时间就是duration时间；
  // gui
  //   .add(clipAction, "time", 0, 4)
  //   .name("模拟进度条")
  //   .step(1)
  //   .onChange((val: number) => {
  //     console.log('val', val);
  //     clipAction.time = val;
  //   });
  
  // 变形动画
  const twistingObj = { t1: 0, t2: 0 };
    gui
        .add(twistingObj, "t1", 0, 1)
        .name("变高")
        .step(0.1)
        .setValue(twistingMesh.morphTargetInfluences![0])
        .onChange((val) => {
            twistingMesh.morphTargetInfluences![0] = val;
        });

    gui
        .add(twistingObj, "t2", 0, 1)
        .name("变宽")
        .step(0.1)
        .setValue(twistingMesh.morphTargetInfluences![1])
        .onChange((val) => {
            twistingMesh.morphTargetInfluences![1] = val;
        });
};

const addToolsToScene = () => {
  controls = new OrbitControls(camera, renderer.domElement);

  // 辅助观察的坐标系
  const axesHelper = new THREE.AxesHelper(100);
  scene.add(axesHelper);

  // 添加一个辅助网格地面，1参是size，一般比创建的几何体大；
  const gridHelper = new THREE.GridHelper(600, 25);
  scene.add(gridHelper);

  //光源设置
  const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
  directionalLight.position.set(100, 60, 50);
  scene.add(directionalLight);

  const ambient = new THREE.AmbientLight(0xffffff, 0.4);
  scene.add(ambient);
};

// 渲染循环
const render = () => {
  controls.update();
  renderer.render(scene, camera);
  const timeGap = clock.getDelta(); // 获取每次帧动画执行的时间间隔
  mixer?.update(timeGap); // 更新播放器mixer时间数据
  requestAnimationFrame(render);
};

onMounted(() => {
  init();
  addMeshToScene();
  addToolsToScene();
  setGui();
  render();
});

onBeforeUnmount(() => renderer.dispose());
</script>

<style scoped>
.btn-wrapper {
  position: absolute;
  left: 20px;
  top: 20px;
}
.btn-wrapper button {
  margin-right: 20px;
}
</style>
```

### 关键帧动画

```TYPESCRIPT
// 添加关键帧动画
const addKeyFrameAnimation = (mesh: THREE.Mesh<THREE.BoxGeometry>) => {
  mesh.name = "BOX"; // 给模型命名
  const seconds = [0, 2, 4]; // 第0, 2, 4秒;
  const positionValues = [
    0,
    0,
    0, // 第0秒位置

    100,
    0,
    0, // 第2秒位置

    0,
    0,
    100, // 第4秒位置
  ];

  // 创建关键帧数据
  const positionKF = new THREE.KeyframeTrack(
    "BOX.position",
    seconds,
    positionValues
  );

  // 创建关键帧数据关于颜色
  const colorKF = new THREE.KeyframeTrack(
    "BOX.material.color",
    [2, 4], // 第2 和 4秒
    [1, 0, 0, 0, 1, 0] // 第2、4秒对应的rgb颜色
  );

  // 创建关键帧动画，name:关键帧名称，duration:动画总时长 (应该设置得比关键帧的时间点长)，tracks:关键帧数据
  clip = new THREE.AnimationClip("test", duration, [positionKF, colorKF]);

  // 创建动画播放器mixer， 包含了关键帧动画的模型对象作为AnimationMixer的参数
  mixer = new THREE.AnimationMixer(mesh);

  clipAction = mixer.clipAction(clip); // 返回动画动作对象
  clipAction.loop = THREE.LoopOnce; // 设置播放一次就停止; 默认循环播放 THREE.LoopRepeat;
  clipAction.clampWhenFinished = true; // 物体状态在动画结束后自动暂停，默认false；需先设置LoopOnce；

  clipAction.play(); // play动画播放，默认循环播放
  mixer.addEventListener("finished", onFinished);
};
```

### 模型动画

```TYPESCRIPT
const loader = new GLTFLoader(); // 创建一个GLTF加载器

const addModelAnimation = () => {
  loader.load("/3D/car.glb", function (gltf) {
    console.log("duration of car:", gltf.animations[0].duration);

    gltf.scene.scale.set(0.1, 0.1, 0.1);
    gltf.scene.position.set(-70, 0, 0);
    scene.add(gltf.scene);

    // 创建动画播放器mixer
    carMixer = new THREE.AnimationMixer(gltf.scene);
    carMixer.clipAction(gltf.animations[0]).play();

    const clock = new THREE.Clock();
    function loop() {
      requestAnimationFrame(loop);
      carMixer?.update(clock.getDelta());
    }
    loop();
  });
};
```

### 变形动画

```TYPESCRIPT
let twistingMesh: THREE.Mesh<THREE.BoxGeometry>;

const addTwistingAnimation = () => {
  const geometry = new THREE.BoxGeometry(20, 20, 20);
  const target1 = new THREE.BoxGeometry(20, 50, 20).attributes.position; // 变高
  const target2 = new THREE.BoxGeometry(20, 20, 50).attributes.position; // 变宽
  geometry.morphAttributes.position = [target1, target2]; // 几何体变形目标数据，需在new Mesh之前;

  const material = new THREE.MeshLambertMaterial();
  twistingMesh = new THREE.Mesh(geometry, material);
  twistingMesh.name = "twistingBox";
  twistingMesh.position.set(0, 100, 0);
  scene.add(twistingMesh);

  // twistingMesh.morphTargetInfluences![0] = 1; // 变形权重0 - 1；与其他权重相同时，每个都会取值；
  // twistingMesh.morphTargetInfluences![1] = 1; // 相同时，变形取值按最大1占比，如 0.3 等于 30%；

  const KF1 = new THREE.KeyframeTrack(
    "twistingBox.morphTargetInfluences[0]",
    [0, 2], // 第0到2秒
    [0, 1] // 从0到3秒变化的权重值
  );

  const KF2 = new THREE.KeyframeTrack(
    "twistingBox.morphTargetInfluences[1]",
    [2, 3], // 第2到3秒
    [0, 1] // 从2到3秒变化的权重值
  );

  const clip = new THREE.AnimationClip("twist", duration, [KF1, KF2]);
  const twistingMixer = new THREE.AnimationMixer(twistingMesh);
  const clipAction = twistingMixer.clipAction(clip); // 返回动画动作对象
  clipAction.loop = THREE.LoopOnce; // 设置播放一次就停止; 默认循环播放 THREE.LoopRepeat;
  clipAction.clampWhenFinished = true; // 物体状态在动画结束后自动暂停，默认false；需先设置LoopOnce；
  clipAction.play();

  const clock = new THREE.Clock();
  function loop() {
    requestAnimationFrame(loop);
    twistingMixer?.update(clock.getDelta());
  }
  loop();
};
```
