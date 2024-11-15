---
title: three_geometry
---

> three.js：http://www.webgl3d.cn/pages/aac9ab/
> threejs 进阶：http://www.webgl3d.cn/pages/001888/
> webgpu：http://www.webgl3d.cn/pages/9bbfa9/
> webgl：http://www.webgl3d.cn/WebGL/

### 概念

- three.js 中的几何体都是基于 **BufferGeometry 类** 构建的，BufferGeometry 是一个没有任何形状的空几何体，通过定义 **顶点数据** 自定义几何体形状；

- 顶点数据通过 **类型化数组 Float32Array** 创建一组 xyz 坐标数据用来表示几何体的顶点数据；

- three.js 中的几何体的面都是由三角形构成，一个三角形有 3 个顶点绘制而成，每个顶点有 3 个值形成一个坐标（x、y、z）

- 如正方形的一个面由 2 个三角形构成，一个面有 4 个顶点，6 个面就是 24 个顶点（几何体.attributes.position.count）；

```typescript index.vue
<template>
  <div ref="containerRef" style="width: 100%; height: 100vh"></div>
</template>

<script lang="ts" setup>
import * as THREE from "three";
import { ref, onMounted, onBeforeUnmount } from "vue";
import { OrbitControls } from "three/addons/controls/OrbitControls.js";
import model from "./model/model.ts";

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

  // 将模型对象添加到场景中
  scene.add(model);
  addToScene();
};

const addToScene = () => {
  // 添加一个辅助网格地面，1参是size，一般比创建的几何体大；
  const gridHelper = new THREE.GridHelper(600, 25);
  scene.add(gridHelper);

  // 辅助观察的坐标系
  const axesHelper = new THREE.AxesHelper(60);
  scene.add(axesHelper);

  // 平行光
  const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
  directionalLight.position.set(100, 60, 50);
  scene.add(directionalLight);

  // 环境光
  const ambient = new THREE.AmbientLight(0xffffff, 0.4);
  scene.add(ambient);

  // 轨道控制器
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

### Float32Array 创建顶点数据

```typescript point.ts
import * as THREE from "three";

/**
    三维向量Vector3方式 定义坐标值
    const pointsArr = [
        new THREE.Vector3(0,0,0),
        new THREE.Vector3(0,100,0),
        new THREE.Vector3(0,100,100),
        new THREE.Vector3(0,0,100),
    ];

    把数组pointsArr里面的坐标数据提取出来，赋值给`geometry.attributes.position`属性
    const geometry = new THREE.BufferGeometry();
    geometry.setFromPoints(pointsArr);
*/

// 定义 类型化数组创建顶点数据
const vertices = new Float32Array([
  50,
  0,
  0, //顶点1坐标
  0,
  100,
  0, //顶点2坐标
  0,
  0,
  100, //顶点3坐标
  50,
  0,
  50, //顶点4坐标
]);

// 创建 属性缓冲区对象；1参传入顶点数据，2参表示3个数据描述一个顶点
const attribute = new THREE.BufferAttribute(vertices, 3);
const geometry = new THREE.BufferGeometry(); // 创建 几何体对象
geometry.attributes.position = attribute; // 设置几何体顶点位置属性

// 创建 每个顶点颜色数据
const colors = new Float32Array([
  1,
  0,
  0, //顶点1颜色；3个参数对应rgb；
  0,
  0,
  1, //顶点2颜色
  0,
  1,
  0, //顶点3颜色
  0.3,
  0.5,
  0.8, //顶点4颜色
]);

// 设置几何体attributes属性的颜色color属性；3个为一组,表示一个顶点的颜色数据RGB
geometry.attributes.color = new THREE.BufferAttribute(colors, 3);

export default geometry;
```

### Points 点模型

### geometry.attributes.color 点颜色

### PointsMaterial 点材质

![](/images/three/geometry/point1.png)

```typescript model.ts
import geometry from "./point.ts";

// 创建 点材质; 材质参数参考基类Material属性；
const material = new THREE.PointsMaterial({
  // color: 0xffffff, // 所有顶点颜色
  vertexColors: true, // 使用自定义数据渲染顶点颜色；默认false；
  size: 10.0, // 正方体顶点的尺寸px
});

// 创建 点模型对象
const model = new THREE.Points(geometry, material);

export default model;
```

---

### Line 线模型

### LineBasicMaterial 线材质

### LineLoop 闭合线条模型

### LineSegments 非连续的线条模型

![](/images/three/geometry/point2.png)

```typescript model.ts
import * as THREE from "three";
import geometry from "./point";

// 创建 线材质
const material = new THREE.LineBasicMaterial({
  //   color: 0xffffff, // 所有颜色
  vertexColors: true, // 使用自定义数据渲染顶点颜色；默认false；
});

// 线条模型
const model = new THREE.Line(geometry, material);

// 闭合线条模型
// const model = new THREE.LineLoop(geometry, material);

// 非连续的线条模型
// const model = new THREE.LineSegments(geometry, material);

export default model;
```

---

### 顶点索引 geometry.index

### 顶点法线 geometry.attributes.normal

![](/images/three/geometry/point3.png)

```typescript model.ts
import * as THREE from "three";

/**
 *  创建顶点, 用两个三角形构成一个面;
    - 逆时针描述正面时，从反面看不到；顺时针描述反面时，从正面看不到
    - 3个值(x、y、z ) 描述一个点， 9个值构成一个三角形；
    - 每个顶点的代码可以无序，只要能组成一个三角形；

    //  定义 类型化数组创建顶点数据
    const vertices = new Float32Array([
        0, 50, 0,  // 顶点1, 下标0, 第一个三角形;
        0, 0, 0,
        50, 0, 0,

        50, 0, 0, // 第二个三角形 (重复顶点)
        50,50, 0,
        0, 50, 0  // (重复顶点)
    ]);

    // 创建 属性缓冲区对象；1参传入顶点数据，2参表示3个数据描述一个顶点
    const attribute = new THREE.BufferAttribute(vertices, 3);
    geometry.attributes.position = attribute;
*/

//  定义 类型化数组创建顶点数据；使用顶点索引时，需将相邻顶点移除
const vertices = new Float32Array([
  0,
  0,
  0, // 顶点1，下标0
  50,
  0,
  0, // 顶点2，下标1
  50,
  50,
  0, // 顶点3，下标2
  0,
  50,
  0, // 顶点4，下标3
]);

// 创建 属性缓冲区对象；1参顶点数据，2参表示几个数据描述一个顶点
const attribute = new THREE.BufferAttribute(vertices, 3);

const geometry = new THREE.BufferGeometry();

// 设置几何体顶点位置属性
geometry.attributes.position = attribute;

// 使用下标连接顶点构成面；
const indexes = new Uint16Array([0, 1, 2, 2, 3, 0]);

// 设置几何体顶点索引
geometry.index = new THREE.BufferAttribute(indexes, 1);

// 每个顶点的法线数据 和 顶点位置数据对应
const normals = new Float32Array([
  0,
  0,
  1, // 顶点1 法线/法向量
  0,
  0,
  1, // 顶点2
  0,
  0,
  1, // 顶点3
  0,
  0,
  1, // 顶点4
]);

// 设置几何体的顶点法线；3个数据表示一个顶点的法线数据；根据法线影响 受光照影响的几何体 的光照程度；
geometry.attributes.normal = new THREE.BufferAttribute(normals, 3);

// 创建 每个顶点颜色数据；每3个参数对应rgb；
const colors = new Float32Array([
  1,
  0,
  0, //顶点1颜色
  0,
  0,
  1, //顶点2颜色
  0,
  1,
  0, //顶点3颜色
  0.3,
  0.5,
  0.8, //顶点4颜色
]);

geometry.attributes.color = new THREE.BufferAttribute(colors, 3);

const material = new THREE.MeshBasicMaterial({
  //   color: 0x00ffff, //材质颜色
  vertexColors: true, //默认false，设置为true表示使用颜色渲染
  side: THREE.DoubleSide, //两面可见
  wireframe: true,
});

// 网格模型
const model = new THREE.Mesh(geometry, material);

export default model;
```

### 几何中心

### 辅助坐标系

scene.add(new THREE.AxesHelper(40)); // 给场景添加辅助坐标系（唯一）
mesh.add(new THREE.AxesHelper(40)); // 给模型添加辅助坐标系（基于几何中心）

### 局部坐标系

局部坐标，mesh/group.position.set, mesh/group.translate 都会造成坐标移动 (不包含 geometry.translate) 移动的位置；

### 全局坐标系

世界坐标，模型的局部坐标 （不是原点坐标）移动的 position 累加上 嵌套的所有父级的移动 position/translate；

![](/images/three/geometry/point5.png)

```typescript model.ts
import * as THREE from "three";

const geometry = new THREE.BoxGeometry(50, 50, 50);
const material = new THREE.MeshLambertMaterial({ color: 0x00ffff });
const mesh = new THREE.Mesh(geometry, material);
mesh.add(new THREE.AxesHelper(40)); // 给模型添加辅助坐标系（基于几何中心）

/**
原点
    初始化时，模型原点(0, 0, 0), 原点在模型自身长/宽/高 的一半位置；
    辅助坐标系，初始化时，同模型原点一致，不跟随模型移动，始终在初始原点上；
    注意：position是对象引用类型，打印position是最终未知；

几何中心
    如translate向X平移，绕Y轴旋转时，是基于原点旋转，不是平移后绕着几何体中心旋转；
    注意：几何体的原点不跟随几何中心改变；
 */

geometry.translate(25, 0, 0); // 几何体朝x轴移动自身的一半25距离；
mesh.rotateY(Math.PI / 3); // 模型绕Y轴旋转45度，模型绕原点位置旋转，不是自身的几何中心旋转；
console.log("原点", mesh.position); // {x: 0, y: 0, z: 0}，原点不受translate影响

/**
 局部坐标 移动模型position （移到原点）
*/
mesh.position.set(0, 25, 0); // 等价于 mesh.translateY(25);
console.log("局部坐标", mesh.position); // {x: 0, y: 25, z: 0}

const group = new THREE.Group();
group.add(mesh);
group.position.set(50, 0, 0);

const worldPosition = new THREE.Vector3();
mesh.getWorldPosition(worldPosition); // 获取mesh世界坐标

/**
 *  世界坐标
 *  模型自身的中心坐标 （不是原点坐标）移动距离 累加上 所有Group嵌套父级的移动position；
 */
console.log("世界坐标", worldPosition); // {x: 50, y:25, z: 0}

export default group;
```

![](/images/three/geometry/point4.png)

```typescript model.ts
import * as THREE from "three";

// 创建组
const group = new THREE.Group();
group.name = "小区";

const group1 = new THREE.Group();
group1.name = "高层";

// 创建多个网格模型
for (let i = 0; i < 3; i++) {
  const geometry = new THREE.BoxGeometry(20, 60, 20);
  const material = new THREE.MeshLambertMaterial({ color: 0x00ffff });
  const mesh = new THREE.Mesh(geometry, material);
  mesh.position.x = i * 30; // 沿着x轴方向阵列 (局部坐标)
  mesh.name = `高层-${i + 1}`; // 添加名字

  group1.add(mesh); // 添加到组中
}

// 根据box的长/高/宽 除以2的长度，平移到原点上；(局部坐标)
// group1.position.set(10, 30, 10);
group1.translateX(10);
group1.translateY(30);
group1.translateZ(10);

group.position.set(10, 0, -20); // 组沿轴平移；(局部坐标)
group.add(group1);

// 树结构递归所有子级
group.traverse((item) => {
  if (item.type === "Mesh") {
    console.log(item.name);
    item.material.color.set(0xffff00);
  }
});

// 根据名字选择模型节点
const currentMesh = group.getObjectByName("高层-1")!;
currentMesh.material.color.set(0xff0000);

// 获取模型的世界坐标
const worldPosition = new THREE.Vector3();
// currentMesh.position.x = 10; // 如这里赋值是累加x，世界坐标也会累加，不是覆盖；

currentMesh.getWorldPosition(worldPosition);
currentMesh.add(new THREE.AxesHelper(40));
console.log("高层-1 的世界坐标", worldPosition); // {x: 20, y: 30, z: -10}

/**
 x = group1 10 + group 10；
 y = group1 30 + group 0；
 z = group1 10 + group -20；
*/

export default group;
```

---

### 顶点自定义圆

![](/images/three/geometry/point6.png)

```typescript model.ts
import * as THREE from "three";

const geometry = new THREE.BufferGeometry(); //创建一个几何体对象
const R = 50; // 圆弧半径
const N = 20; // 分段数量

// 批量生成圆弧上的顶点数据
const arr = [];

// 两个相邻点间隔弧度
// const sp = (2 * Math.PI) / N; // 完整圆弧
const sp = (1 * Math.PI) / N; // 半圆弧

for (let i = 0; i < N + 1; i++) {
  const angle = sp * i;

  // 以坐标原点为中心，在XOY平面上生成圆弧上的顶点数据
  const x = R * Math.cos(angle); // 返回余弦值
  const y = R * Math.sin(angle); // 返回正弦值
  arr.push(x, y, 0);
}

// 移动圆位置
// const cx = 200;
// const cy = 100;
// for (let i = 0; i < N + 1; i++) {
//   const angle = sp * i; //当前点弧度
//   const x = cx + R * Math.sin(angle);
//   const y = cy + R * Math.cos(angle);
//   arr.push(x, y, 0);
// }

//类型数组创建顶点数据
const vertices = new Float32Array(arr);

// 创建属性缓冲区对象
const attribue = new THREE.BufferAttribute(vertices, 3); //3个为一组，表示一个顶点的xyz坐标

// 设置几何体attributes属性的位置属性
geometry.attributes.position = attribue;

const pointMaterial = new THREE.PointsMaterial({
  color: 0xff00ff, //线条颜色
  size: 10,
});

const points = new THREE.Points(geometry, pointMaterial);

// 线材质
const lineMaterial = new THREE.LineBasicMaterial({
  color: 0xff0000, //线条颜色
});

// 创建线模型
const line = new THREE.Line(geometry, lineMaterial);

const group = new THREE.Group();
group.add(points, line);

export default group;
```

### ArcCurve 圆

### EllipseCurve 椭圆

![](/images/three/geometry/point7.png)

```typescript model.ts
import * as THREE from "three";

const curve = new THREE.EllipseCurve(
  0,
  0, // x，y 圆弧中心坐标
  100,
  30, //  x半径，y半径 (y半径0时 等于 x半径)
  0,
  2 * Math.PI, // 从0角度开始绘制，到360度结束
  false // 默认false；逆时针绘制
);

// 返回51个顶点数据；将曲线划分成 vector2二位向量 对象数组；
// const pointsArr = curve.getPoints(50); // 获取曲线间距点（曲率；弯曲部分间距短）
const pointsArr = curve.getSpacedPoints(50); // 获取曲线间距点（同等距离）
const geometry = new THREE.BufferGeometry();
geometry.setFromPoints(pointsArr); // 把顶点数据pointsArr赋值 geometry.attributes.position 属性

// ArcCurve 弧线
const curve2 = new THREE.ArcCurve(
  0,
  0, // x，y 圆弧中心坐标
  60, //  弧线半径
  0,
  2 * Math.PI, // 从0角度开始绘制，到360度结束
  false // 默认false；逆时针绘制
);
const geometry2 = new THREE.BufferGeometry();
const pointsArr2 = curve2.getSpacedPoints(50); // 获取曲线间距点（同等距离）
geometry2.setFromPoints(pointsArr2);

const material = new THREE.LineBasicMaterial();
const ellipseLine = new THREE.Line(geometry, material);
const arcLine = new THREE.Line(geometry2, material);

const groud = new THREE.Group();
groud.add(ellipseLine, arcLine);

export default groud;
```

### CatmullRomCurve3 三维样条曲线

![](/images/three/geometry/point8.png)

```typescript model.ts
import * as THREE from "three";

// 三维向量Vector3创建一组顶点坐标
const arr = [
  new THREE.Vector3(-50, 20, 50),
  new THREE.Vector3(-10, 40, 40),
  new THREE.Vector3(0, 0, 0),
  new THREE.Vector3(60, -10, 0),
  new THREE.Vector3(70, 20, 30),
];
// 三维样条曲线表达不规则的曲线
const curve = new THREE.CatmullRomCurve3(arr);

/**
 * 二维样条曲线顶点坐标
 * const arr = [
    new THREE.Vector2(-50, 20),
    new THREE.Vector2(-10, 40),
    new THREE.Vector2(0, 0),
    new THREE.Vector2(60, -60),
    new THREE.Vector2(70, 0),
  ];

  const curve = new THREE.SplineCurve(arr);
*/

// 曲线上获取点， 曲线间距点
const pointsArr = curve.getPoints(100);
const geometry = new THREE.BufferGeometry();
geometry.setFromPoints(pointsArr); //读取坐标数据赋值给几何体顶点

// 线材质
const material = new THREE.LineBasicMaterial({
  color: 0x00fffff,
});

// 线模型
const curveLine = new THREE.Line(geometry, material);

// 用点模型可视化样条曲线经过的顶点位置
const pointGgeometry = new THREE.BufferGeometry();
pointGgeometry.setFromPoints(arr);

const pointMaterial = new THREE.PointsMaterial({
  color: 0xff00ff,
  size: 10,
});

//点模型对象
const curvePoints = new THREE.Points(pointGgeometry, pointMaterial);

// 案例 利用三维样条曲线绘制 飞行轨迹
const p1 = new THREE.Vector3(-100, 0, -100);
const p3 = new THREE.Vector3(100, 0, 100);
const centerX = (p1.x + p3.x) / 2; // 计算p1和p3的中点坐标
const centerY = (p1.z + p3.z) / 2;
const h = 50;
const p2 = new THREE.Vector3(centerX, h, centerY);
const curve2 = new THREE.CatmullRomCurve3([p1, p2, p3]);
const pointsArr2 = curve2.getPoints(100); //曲线上获取点
const geometry2 = new THREE.BufferGeometry();
geometry2.setFromPoints(pointsArr2); //读取坐标数据赋值给几何体顶点
const curveLine2 = new THREE.Line(geometry2, material);

const group = new THREE.Group();
group.add(curveLine, curveLine2, curvePoints);

export default group;
```

### CubicBezierCurve 二维贝塞尔曲线

### CubicBezierCurve3 三维贝塞尔曲线

![](/images/three/geometry/point9.png)

```typescript model.ts
import * as THREE from "three";

// p1、p4 起始点和结束点，p2、p3 中间点是曲线的控制点
const p1 = new THREE.Vector3(-80, 0, -10);
const p2 = new THREE.Vector3(-40, 50, 0);
const p3 = new THREE.Vector3(50, 50, 0);
const p4 = new THREE.Vector3(80, 0, 20);

// 三维三次贝赛尔曲线
const curve = new THREE.CubicBezierCurve3(p1, p2, p3, p4);

/**
 *  二维三次贝赛尔曲线 (没有纵向)
    const p1 = new THREE.Vector2(-80, 0);
    const p2 = new THREE.Vector2(-40, 50);
    const p3 = new THREE.Vector2(50, 50);
    const p4 = new THREE.Vector2(80, 0);

    const curve = new THREE.CubicBezierCurve(p1, p2, p3, p4); 
 */

const pointsArr = curve.getPoints(100); //曲线上获取点

const geometry = new THREE.BufferGeometry();
// 读取坐标数据赋值给几何体顶点
geometry.setFromPoints(pointsArr);

const material = new THREE.LineBasicMaterial({
  color: 0x00fffff,
});

const line = new THREE.Line(geometry, material);

// 可视化p1、p2、p3、p4三个点的位置，并用直线相连接（便于观察贝塞尔曲线的绘制规律）
const geometry2 = new THREE.BufferGeometry();
geometry2.setFromPoints([p1, p2, p3, p4]);

// 点构成的线条
const line2 = new THREE.Line(geometry2, new THREE.LineBasicMaterial());

const group = new THREE.Group();
group.add(line, line2);

// 案例 利用贝塞尔曲线绘制飞行轨迹
// p1、p3轨迹线起始点坐标
const p10 = new THREE.Vector3(-100, 0, -100);
const p30 = new THREE.Vector3(100, 0, 100);
// 计算p1和p3的中点坐标
const x2 = (p10.x + p30.x) / 2;
const z2 = (p10.z + p30.z) / 2;
const h = 100;
const p20 = new THREE.Vector3(x2, h, z2);
// 三维二次贝赛尔曲线
const curve2 = new THREE.QuadraticBezierCurve3(p10, p20, p30);
const pointsArr2 = curve2.getPoints(100); //曲线上获取点
const planeGeometry = new THREE.BufferGeometry();
planeGeometry.setFromPoints(pointsArr2); //读取坐标数据赋值给几何体顶点

// 线模型
const planeLine = new THREE.Line(planeGeometry, material);
group.add(planeLine);

export default group;
```

### LineCurve 二维线段曲线

![](/images/three/geometry/point10.png)

```typescript model.ts
import * as THREE from "three";

const R = 80; //圆弧半径
const H = 200; //直线部分高度

// 二维线段曲线；左侧直线 参数需二维向量(v1: 起点，v2：终点)；两个参数代表直线
const leftLine = new THREE.LineCurve(
  new THREE.Vector2(-R, 0), // 开始点（-80, 0)
  new THREE.Vector2(-R, H) // 结束点（-80, 200)
);![alt text](image.png)

// 二维线段曲线；右侧直线 参数需二维向量(v1: 起点，v2：终点)；两个参数代表直线
const rightLine = new THREE.LineCurve(
  new THREE.Vector2(R, H), // 开始点（80, 200)
  new THREE.Vector2(R, 0) // 结束点（80, 0)
);

// 圆弧
const arc = new THREE.ArcCurve(
  0,
  0, // x，y 圆弧中心坐标
  R, //  弧线半径
  0,
  Math.PI, // 从0角度开始绘制，到180度结束
  true // true顺时针绘制
);

// CurvePath创建一个组合曲线对象
const CurvePath = new THREE.CurvePath();

// 拼接出来U型轮廓曲线；注意：添加顺序必须首位相接；
CurvePath.curves.push(rightLine, arc, leftLine);

// CurvePath获取点；直线部分不会像曲线返回中间多余点，只需要取了起始点。
const pointsArr = CurvePath.getPoints(5);

const geometry = new THREE.BufferGeometry();
geometry.setFromPoints(pointsArr as THREE.Vector2[]); // 读取坐标数据赋值给几何体顶点

const material = new THREE.LineBasicMaterial({
  color: 0x00fffff,
});

// 线模型
const line = new THREE.Line(geometry, material);

// 可视化curve.getPoints从曲线上获取的点坐标
const material2 = new THREE.PointsMaterial({
  color: 0xff00ff,
  size: 10,
});

//点模型对象
const points = new THREE.Points(geometry, material2);

const group = new THREE.Group();
group.add(line, points);

export default group;
```

### TubeGeometry 管道几何体

![](/images/three/geometry/point11.png)

```typescript
import * as THREE from "three";

// 三维样条曲线
// const path = new THREE.CatmullRomCurve3([
//   new THREE.Vector3(-50, 20, 90),
//   new THREE.Vector3(-10, 40, 40),
//   new THREE.Vector3(0, 0, 0),
//   new THREE.Vector3(60, -60, 0),
//   new THREE.Vector3(70, 0, 80),
// ]);

// LineCurve3 三维线段曲线；创建直线段路径
const path = new THREE.LineCurve3(
  new THREE.Vector3(0, 100, 0),
  new THREE.Vector3(0, 0, 0)
);

/**
 * TubeGeometry
 * path:路径，40：沿着轨迹细分数，2：管道半径，25：管道截面圆细分数
 *
 */
const geometry = new THREE.TubeGeometry(path, 40, 2, 25);

const material = new THREE.MeshLambertMaterial({
  color: 0x00ffff,
  side: THREE.DoubleSide, //双面显示管道内壁
});

const mesh = new THREE.Mesh(geometry, material);

// 创建多段线条的顶点数据
const p1 = new THREE.Vector3(0, 0, 100);
const p2 = new THREE.Vector3(0, 0, 30);
const p3 = new THREE.Vector3(0, 0, 0);
const p4 = new THREE.Vector3(30, 0, 0);
const p5 = new THREE.Vector3(100, 0, 0);
// 3D直线线段
const leftLine = new THREE.LineCurve3(p1, p2);
// 三维二次贝塞尔曲线
const curve = new THREE.QuadraticBezierCurve3(p2, p3, p4);
// 3D直线线段
const rightLine = new THREE.LineCurve3(p4, p5);

const CurvePath = new THREE.CurvePath();
// 三条线拼接为一条曲线
CurvePath.curves.push(leftLine, curve, rightLine);

// CurvePath:路径   40：沿着轨迹细分数  2：管道半径   25：管道截面圆细分数
const geometry2 = new THREE.TubeGeometry(CurvePath, 50, 2, 25);
const mesh2 = new THREE.Mesh(geometry2, material);
mesh.add(mesh2);

export default mesh;
```

### LatheGeometry 车削几何体（绕 y 轴旋转成型）

![](/images/three/geometry/point12.png)

```typescript
import * as THREE from "three";

// Vector2定义轮廓坐标
const pointsArr = [
  new THREE.Vector2(50, 60),
  new THREE.Vector2(25, 0),
  new THREE.Vector2(50, -60),
];

/**
 * LatheGeometry

    pointsArr轮廓绕y轴旋转，生成几何体曲面形状；
    30：旋转圆周方向几何体细分精度
    0, 2 * Math.PI, 绕y轴旋转开始角度和结束角度；默认360；
*/
const geometry = new THREE.LatheGeometry(pointsArr, 30, 0, Math.PI);

const material = new THREE.MeshLambertMaterial({
  color: 0x00ffff,
  side: THREE.DoubleSide, //两面可见
});
const mesh = new THREE.Mesh(geometry, material);

// 二维样条曲线; 比直接定义轮廓曲率更光滑
const curve = new THREE.SplineCurve(pointsArr);

//曲线上获取点,作为旋转几何体的旋转轮廓
const pointsArr2 = curve.getPoints(50);
const geometry2 = new THREE.LatheGeometry(pointsArr2, 30);

const material2 = new THREE.MeshLambertMaterial({
  color: 0x669999,
  side: THREE.DoubleSide, //两面可见
});
const mesh2 = new THREE.Mesh(geometry2, material2);

mesh.add(mesh2);

export default mesh2;
```

### Shape 二维多边形状

### Shape & ShapeGeometry 二维多边形轮廓 生成平面几何体

### Shape & ExtrudeGeometry 二维多边形坐标轮廓 生成 立方几何体

![](/images/three/geometry/point13.png)

```typescript
import * as THREE from "three";

// 多边形轮廓坐标
const pointsArr = [
  new THREE.Vector2(-50, -50),
  new THREE.Vector2(-60, 0),
  new THREE.Vector2(0, 50),
  new THREE.Vector2(60, 0),
  new THREE.Vector2(50, -50),
];

// Shape表示一个平面多边形轮廓,参数是二维向量构成的数组pointsArr
const shape = new THREE.Shape(pointsArr);

// 多边形shape轮廓作为ShapeGeometry参数，生成一个多边形平面几何体
const geometry = new THREE.ShapeGeometry(shape);

const material = new THREE.MeshLambertMaterial({
  color: 0x00ffff,
  side: THREE.DoubleSide,
});

const mesh = new THREE.Mesh(geometry, material);

// 拉伸
const geometry2 = new THREE.ExtrudeGeometry(
  shape, // 二维轮廓
  {
    depth: 20, // 拉伸宽度；默认1；
    bevelEnabled: false, // 禁止倒角, 默认true; 倒角就是切掉了形状的切角；
    bevelThickness: 5, // 倒角尺寸/宽度: 一般都比较小，且小于拉伸宽度depth；倒角x轴的尺寸；
    // bevelSize: 15, // 倒角尺寸/高度: 倒角y轴的尺寸；
    // bevelSegments: 20, // 倒角细分精度；就是倒角圆润度；等于depth时就是完整的圆润度；等于1时就是直角；
  }
);

const material2 = new THREE.MeshLambertMaterial();

const mesh2 = new THREE.Mesh(geometry2, material2);

mesh2.position.z = 30;
mesh.add(mesh2);

export default mesh;
```

### Shape & ExtrudeGeometry 二维多边形坐标轮廓

生成立方几何体，再根据立方几何体描述样条样式 生成特殊造型；
![](/images/three/geometry/point14.png)

```typescript
import * as THREE from "three";

// 1.描述一个10*10 正方形
const shape = new THREE.Shape([
  new THREE.Vector2(0, 0), //多边形起点
  new THREE.Vector2(0, 10),
  new THREE.Vector2(10, 10),
  new THREE.Vector2(10, 0),
]);

// 2.扫描轨迹：创建轮廓轨迹(3D样条曲线)
const curve = new THREE.CatmullRomCurve3([
  new THREE.Vector3(20, 10, -70),
  new THREE.Vector3(-10, -10, -50),
  new THREE.Vector3(10, 0, 0),
  new THREE.Vector3(8, 50, 50),
  new THREE.Vector3(-5, 0, 100),
]);

//3.扫描造型，根据shape二维正方形生成正方体，使用立方体按照扫描的路径生成特殊造型；
const geometry = new THREE.ExtrudeGeometry(
  shape, // 扫描轮廓
  {
    extrudePath: curve, //扫描轨迹
    steps: 100, // 沿着路径细分精度，越大越光滑
  }
);

const material = new THREE.MeshLambertMaterial({
  color: 0x00ffff,
  // wireframe:true,
});
const mesh = new THREE.Mesh(geometry, material);

export default mesh;
```

### Shape moveTo & lineTo & arc 自定义 二维多边形状

### shape.holes 添加孔洞

![](/images/three/geometry/point15.png)

```typescript
import * as THREE from "three";

const hole1 = new THREE.Path(); // 圆孔1
hole1.absarc(30, 20, 10, 0, 2 * Math.PI);

const hole2 = new THREE.Path(); // 圆孔2
hole2.absarc(80, 20, 10, 0, 2 * Math.PI);

const hole3 = new THREE.Path(); // 方形孔
hole3.moveTo(50, 50);
hole3.lineTo(80, 50);
hole3.lineTo(80, 80);
hole3.lineTo(50, 80);

// 1. 生成shape实例对象时，默认currentPoint当前点在 0, 0
const shape = new THREE.Shape();
shape.holes.push(hole1, hole2, hole3);

// console.log('currentPoint',shape.currentPoint); (0, 0)

// 2. 设置起点；currentPoint默认起始点变为(10, 0)；默认是原点；
shape.moveTo(10, 0);

// 3. 绘制直线线段
shape.lineTo(100, 0); // 以currentPoint为起点 (10, 0)，到结束点 (100, 0) 绘制一条线
shape.lineTo(100, 100); // 从currentPoint (100, 0) 到 (100, 100) 绘制一条线
shape.lineTo(10, 100); // 从currentPoint (100, 100) 到 (10, 100) 绘制一条线；默认最后会闭合起点和终点；

// 4. 生成平面几何体
const geometry = new THREE.ShapeGeometry(shape);

// 4. 拉伸生成的几何体
const geometry2 = new THREE.ExtrudeGeometry(shape, {
  depth: 20, //拉伸长度
  bevelEnabled: false, //禁止倒角
});

const material = new THREE.MeshLambertMaterial({
  color: 0x00ffff,
  side: THREE.DoubleSide,
  //   wireframe: true,
});

const mesh = new THREE.Mesh(geometry, material);
const mesh2 = new THREE.Mesh(geometry2, material);
mesh2.position.z = 100;
mesh.add(mesh2);

// 案例：绘制直线和圆弧
const shape2 = new THREE.Shape();
shape2.holes.push(hole1, hole2);

shape2.lineTo(100, 0); // 以currentPoint为起点(0, 0)，到结束点(100, 0)绘制一条线

// 注意：绘制圆弧arc 参数的圆心0,0坐标是相对当前currentPoint而言，而不是坐标原点；
shape2.arc(0, 0, 50, 0, Math.PI / 2); // 以currentPoint(100, 0)起点，到圆弧线 半径50 旋转90度结束点(100, 50);
// shape2.absarc(100, 0, 50, 0, Math.PI / 2) // 绝对圆弧的x，y不受上一个结束点影响，所以必须手动输入上个结束点；
shape2.lineTo(0, 50); // 以上一个结束点为起点(100, 50)，到结束点 (0, 50)；最后自动闭合；
const geometry3 = new THREE.ShapeGeometry(shape2);
const mesh3 = new THREE.Mesh(geometry3, material);

mesh3.position.z = -100;
mesh.add(mesh3);

// 拉伸生成的几何体
const geometry4 = new THREE.ExtrudeGeometry(shape2, {
  depth: 20, //拉伸长度
  bevelEnabled: false, //禁止倒角
  curveSegments: 20, // 曲线部分的细分度
});

const mesh4 = new THREE.Mesh(geometry4, material);
mesh4.position.z = -200;
mesh.add(mesh4);

export default mesh;
```

### EdgesGeometry 边缘几何体

![](/images/three/geometry/point16.png)

```typescript
import * as THREE from "three";
// import { GLTFLoader } from "three/addons/loaders/GLTFLoader.js";

const boxGeometry = new THREE.BoxGeometry(50, 50, 50);

// 正方体材质颜色
const material = new THREE.MeshLambertMaterial({
  color: 0x004444,
  transparent: true,
  opacity: 0.8,
});

// 网格模型
const meshModel = new THREE.Mesh(boxGeometry, material);

// 创建新的边缘几何体；几何体作为EdgesGeometry参数
const edgeGeometry = new THREE.EdgesGeometry(boxGeometry);

// 线条模型；使用线条模型渲染边缘几何体，根据边缘几何线；LineSegments非连续的线条模型
const lineModel = new THREE.LineSegments(
  edgeGeometry,
  new THREE.LineBasicMaterial()
);

// 圆柱几何体; CylinderGeometry 顶部半径，底部半径，高，半径细分度；
const circleGeometry = new THREE.CylinderGeometry(30, 30, 50, 30);

// 圆柱边缘几何; 2参 当相邻面的法线之间的角度（单位为角度）超过这个值时，才会渲染边缘，默认1；这里40超过了CylinderGeometry的30细分度才会出现边缘；
const circleEdgeGeometry = new THREE.EdgesGeometry(circleGeometry, 40);

// 圆柱线条模型
const cirCleLineModel = new THREE.LineSegments(
  circleEdgeGeometry,
  new THREE.LineBasicMaterial()
);
cirCleLineModel.position.z = 100;

meshModel.add(lineModel, cirCleLineModel);

// const loader = new GLTFLoader(); //创建一个GLTF加载器
// loader.load('../../assets/gltf/contructure.gltf', function (gltf) {
//     // 递归遍历设置每个模型的材质，同时设置每个模型的边线
//     gltf.scene.traverse(function (obj) {
//         if (obj.isMesh) {
//             // 模型材质重新设置
//             obj.material = new THREE.MeshLambertMaterial({
//                 color: 0x004444,
//                 transparent: true,
//                 opacity: 0.5,
//             });
//             // 模型边线设置
//             const edges = new THREE.EdgesGeometry(obj.geometry);
//             const edgesMaterial = new THREE.LineBasicMaterial({
//                 color: 0x00ffff,
//             })
//             const line = new THREE.LineSegments(edges, edgesMaterial);
//             obj.add(line);
//         }
//     });
//     meshModel.add(gltf.scene);
// })

export default meshModel;
```

---

### 视锥体

### renderer 渲染器

### camera 相机

### stats 性能监视器

### gui

![](/images/three/geometry/point0.png)

```javascript
<template>
  <div class="btn-wrapper">
    <button class="btn2" @click="switchFullscreen(true)">全屏</button>
    <button class="btn2" @click="switchFullscreen(false)">退出全屏</button>
    <button class="btn1" @click="onChangeColor('#f00')">red</button>
    <button class="btn2" @click="onChangeColor('#00f')">blue</button>
    <button class="btn2" @click="onDownLoad">下载</button>
  </div>

  <div ref="containerRef" style="width: 100%; height: 100vh"></div>
</template>

<script lang="ts" setup>
import * as THREE from "three";
import { OrbitControls } from "three/addons/controls/OrbitControls.js";
import { GUI } from "three/addons/libs/lil-gui.module.min.js";
import Stats from "three/addons/libs/stats.module.js";
import { onMounted, ref, onBeforeUnmount } from "vue";

let width = 0;
let height = 0;
let containerRef = ref();

const guiConfigObj = {
  amLight: true,
  rotation: false,
  Fullscreen: function () {
    document.body.requestFullscreen();
  },
  exitFullscreen: function () {
    document.exitFullscreen();
  },
};

// 创建3D场景对象 Scene
const scene = new THREE.Scene();
let stats: Stats; // 性能监视器
let controls: OrbitControls; // 相机轨道控制器
let geometry: THREE.BoxGeometry; // 几何体
let material: THREE.MeshLambertMaterial; // 材质
let mesh: THREE.Mesh<THREE.BoxGeometry>; // 网格模型
let camera: THREE.PerspectiveCamera; // 相机
let renderer: THREE.WebGLRenderer; // 渲染器
let ambient: THREE.AmbientLight; // 环境光
let directionalLight: THREE.DirectionalLight; // 平行光

const init = () => {
  width = containerRef.value.clientWidth;
  height = containerRef.value.clientHeight;

  /**
   * 创建 透视投影相机PerspectiveCamera(fov, aspect, near, far)，构成一个四棱台3D空间，被称为视锥体，
   * 只有视锥体之内的物体，才会渲染出来，视锥体范围之外的物体不会显示在Canvas画布上，
   * 比如 相机位置 大于 far远端面的值，几何体就会被才减掉
   *
   * 	fov: 视锥体垂直视野角度 - 垂直的视野角度，默认值50（类似于人眼水平视野角度能观察到约180度；
   * 	aspect: 视锥体长宽比 - 定义相机输出在浏览器上的canvas画布长和宽（单位：px）；比值通常和渲染大小一致，比如render渲染的画布改变，aspect视锥体会被拉伸；
   * 	near: 视锥体距离相机近端面 （相机位置 小于 near 近端面的值，几何体此时在相机的背后看不见）
   * 	far: 视锥体距离相机远端面 （相机位置 大于 far远端面的值，几何体就会被裁剪掉），值越大看到的视野更多，可以和lookAt配合；
   */
  camera = new THREE.PerspectiveCamera(30, width / height, 1, 3000);
  camera.position.set(300, 300, 500); // 相机在三维坐标系中的位置

  // 创建渲染器对象
  renderer = new THREE.WebGLRenderer({
    antialias: true, // 防锯齿
    preserveDrawingBuffer: true, // 是否可以下载canvas图片
    alpha: false, // 场景透明度，使其与背景融合展示悬浮效果；
    logarithmicDepthBuffer: true, // 优化模型深度冲突，另外也可以调整模型之间position不要太近作优化；
  });
  renderer.setSize(width, height);
  renderer.setPixelRatio(window.devicePixelRatio); // 设置屏幕像素比，防止绘图模糊
  renderer.setClearColor(0x444444, 1); //设置背景颜色

  containerRef.value.appendChild(renderer.domElement);

  addMeshToScene();
  addToolsToScene();
  addLightsToScene();
};

const addMeshToScene = () => {
  // 创建网格模型; 创建一个长方体几何对象Geometry；长、宽、高；
  geometry = new THREE.BoxGeometry(50, 50, 50);

  material = new THREE.MeshLambertMaterial({ color: 0x00ffff });

  // 创建网格模型对象 Mesh，将几何体 和 材质作为参数；
  mesh = new THREE.Mesh(geometry, material);

  // 设置网格模型在三维空间中的位置坐标，默认是坐标原点
  mesh.position.set(0, 0, 0);

  // 将网格模型添加到场景中
  scene.add(mesh);

  /**
 * lookAt 
 * 相机观察目标，默认朝向坐标系原点，观察物体处于视野(画布)的中心点；
 * 也可以读取网格模型 并指向 目标位置；

 * 注意：
 *  1. lookAt 朝向的视锥体范围，几何体需在 near 和 far 视锥体范围内，几何体才能被看到；
 *  2. controls.target 也会改变观察目标方向；
 */
  camera.lookAt(mesh.position);
  // camera.lookAt(0, 0, 0); // 相机观察目标，默认朝向坐标系原点；
};

const addToolsToScene = () => {
  // 创建性能监视器
  stats = new Stats();
  stats.showPanel(0); // 0: fps展示帧率, 1: 每帧渲染耗费多少ms, 2: mb, 3: custom;
  // document.body.appendChild(stats.dom); // 输出一个Dom元素；
  containerRef.value.appendChild(stats.dom);

  // 三维坐标系
  const axesHelper = new THREE.AxesHelper(100);
  scene.add(axesHelper);

  // 轨道控制器
  controls = new OrbitControls(camera, renderer.domElement);
  controls.target.set(0, 0, 0); // 相机轨道控制器 默认是基于原点旋转/缩放；
  controls.autoRotate = true; // 是否自动围绕目标旋转 - 需执行update；
  controls.enableDamping = true; // 启用阻尼；
  controls.dampingFactor = 0.1; // 阻尼系数（越大阻尼越强）- 需执行update；

  /**
  * controls.change事件：轨道控制器被用户手动改变时，需要重新渲染器渲染三维场景，否则视图上没有改变
  * controls.addEventListener("change", function () {
      renderer.render(scene, camera); // 执行渲染操作 
      controls.update(); // 必须执行 or setRender函数中调用 controls.update()
    });
  */

  /**
  * 例如：设置相机在场景中的位置；摄像机位置发生改变后，必须调用update更新轨道控制器，否则还是基于原来的相机位置作旋转/缩放；
  * 	camera.position.set( 100, 100, 100 );
  * 	controls.update();

  * 例如：设置轨道控制器基于哪个坐标旋转/缩放；
  * 	controls.target.set(0, 0, 0); 
  * 	controls.update();
  
  * 例如：设置轨道控制器是否自动旋转/阻尼等；
  * 	controls.autoRotate = true;
  * 	controls.dampingFactor = 0.1; 
  * 
      function animate() {
        controls.update(); 		
        renderer.render(scene, camera);
        requestAnimationFrame(animate);
      }
  */
};

const addLightsToScene = () => {
  // 环境光：没有特定方向，照亮整个场景 AmbientLight(color:颜色, intensity: 强度，默认1);
  ambient = new THREE.AmbientLight(0xffffff, 1);
  scene.add(ambient);

  // 平行光
  directionalLight = new THREE.DirectionalLight(0xffffff, 1);
  directionalLight.position.set(100, 60, 50);
  scene.add(directionalLight);
  // 可视化平行光
  const dirLightHelper = new THREE.DirectionalLightHelper(
    directionalLight,
    5,
    0xff0000
  );
  scene.add(dirLightHelper);

  /**
   * 点光源 new THREE.PointLight(color, intensity, distance, decay)
   * 	color: 光照颜色
   * 	intensity: 光照强度，默认1.0
   * 	distance: 照射距离
   * 	decay: 光源衰减值，随着距离越长光源越弱，默认是2，0则不衰减
   *
   * position 光源位置，默认是坐标原点
   * 	注意：光源位置和照射距离 与 几何体的大小和坐标位置 有直接影响；
   *
   * 	例如：
   * 	几何体大小50 x 50（实际位置是X/Y轴 -25和25, 几何体是基于原点长/宽/高平等延伸；
   *  光照距离position在x轴125，照射距离distance是100， 所以 125光源正好在几何体上(125-100 = 25) 大于此值就不会显示出来；
   */
  const pointLight = new THREE.PointLight(0xffffff);
  pointLight.distance = 100;
  pointLight.decay = 0;
  pointLight.position.set(100, 0, 0);
  scene.add(pointLight);
  // 可视化点光源：PointLightHelper( light : 要模拟哪个光源, sphereSize : 点光源球形的尺寸, color : 可视化颜色 )
  const pointLightHelper = new THREE.PointLightHelper(pointLight, 5);
  scene.add(pointLightHelper);
};

const setGui = () => {
  const gui = new GUI();
  gui.domElement.style.top = "10px";
  gui.domElement.style.right = "10px";
  gui.domElement.style.width = "300px";

  /**
   * gui.add 创建一个UI交互界面，1参对象，2参对象属性；
   * 是否有3/4参数，gui会根据属性自动生成；
   * 传3和4参，gui生成范围；
   * 传数组，生成下拉列表
   */
  gui.add(guiConfigObj, "Fullscreen").name("全屏");
  gui.add(guiConfigObj, "exitFullscreen").name("退出全屏");
  gui.add(guiConfigObj, "rotation").name("是否旋转");
  gui
    .add(guiConfigObj, "amLight", {
      open: true,
      close: false,
    })
    .name("是否环境光")
    .onChange((val) => {
      if (val) {
        ambient.intensity = 1;
      } else {
        ambient.intensity = 0;
      }
    });

  gui.add(ambient, "intensity", 0, 2.0).name("环境光强度");
  gui.add(directionalLight, "intensity", 0, 2.0).name("平行光强度");
  // 自动根据属性boolean类型 展示选择框；
  gui.add(material, "wireframe").name("是否线框");

  // 设置目录包含控件；
  const folder = gui.addFolder("小立方体");
  folder.close(); // 初始关闭状态
  folder
    .addColor({ cubeColor: "#ff0000" }, "cubeColor")
    .name("颜色")
    .onChange((val: string) => {
      mesh.material.color.set(val);
    });

  const positionFolder = folder.addFolder("位置");

  positionFolder
    .add(mesh.position, "x")
    .name("X轴")
    .min(-1000)
    .max(1000)
    .step(5)
    .onChange((val: number) => {
      console.log("x轴移动了", val);
    });

  positionFolder
    .add(mesh.position, "y")
    .name("Y轴")
    .min(-1000)
    .max(1000)
    .step(5)
    .onFinishChange((val: number) => {
      console.log("y轴移动到了", val);
    });

  positionFolder
    .add(mesh.position, "z")
    .name("Z轴")
    .min(-1000)
    .max(1000)
    .step(5);
};

// 改变颜色
const onChangeColor = (val: string) => {
  mesh.material.color.set(val);
};

// 下载canvas图片
const onDownLoad = () => {
  const link = document.createElement("a");
  const canvas = renderer.domElement;
  link.href = canvas.toDataURL("image/png");
  link.download = "three.png";
  link.click();
};


function setRender() {
  stats.update();
  if (guiConfigObj.rotation) mesh.rotation.y += 0.01; // 设置物体绕着Y轴旋转
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

// 切换全屏
const switchFullscreen = (type: boolean) => {
  if (type) {
    document.body.requestFullscreen();
  } else {
    document.exitFullscreen();
  }
};

onMounted(() => {
  init();
  setGui();
  setResize();
  setRender();
});

onBeforeUnmount(() => renderer.dispose());
</script>

<style scoped>
.btn-wrapper {
  position: absolute;
  left: 20px;
  bottom: 20px;
}
.btn-wrapper button {
  margin-right: 20px;
}
</style>
```