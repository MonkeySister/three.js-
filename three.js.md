## Three.js学习笔记

### 1.Three.js

Three.js是一个3D库，three.js使用WebGL来绘制3D,它处理诸如场景，灯光，阴影，材质，纹理，3d数学之类的东西 。

基本原理及组成：

![](img\2068504-a4d7da33ff9a4c40.webp)

### 2.WebGL 

WebGL是一个非常低级的系统，只能绘制点，线和三角形。

WebGL是一种3D绘图标准，这种绘图技术标准允许把JavaScript和OpenGL ES 2.0结合在一起，通过增加OpenGL ES 2.0的一个JavaScript绑定，WebGL可以为HTML5 Canvas提供硬件3D加速渲染，这样Web开发人员就可以借助系统显卡来在浏览器里更流畅地展示3D场景和模型了，还能创建复杂的导航和数据视觉化。显然，WebGL技术标准免去了开发网页专用渲染插件的麻烦，可被用于创建具有复杂3D结构的网站页面，甚至可以用来设计3D网页游戏等等。

### 3. Three.js中3个重要元素场景、相机、渲染器

**场景(scene)**

场景是所有物体的容器。

```js
var scene = new THREE.Scene();
```

 场景组件包括：

相机——决定哪些东西要渲染到屏幕上；

光源——对材质如何显示及阴影生成产生影响；

物体——Mesh对象，在相机视图里主要的渲染对象，如方块、圆等。

 场景的属性fol雾化和overrideMaterial材质覆盖：

```js

//雾化效果 
//1-线性雾，密度随着距离的增加呈线性增长。参数为：雾的颜色，开始的地方，浓度的加深程度。
scene.fog = new THREE.Fog(0xffffff, 0.015, 100); 
//2-指数雾，密度随距离呈指数级增长。参数为：雾的颜色，浓度
scene.fog = new THREE.Fog(0xffffff, 100); 
 
//材质覆盖：所有物体设置为同样的材质
scene.overrideMaterial = new THREE.MeshLambertMaterial({color: 0xffffff});
```

 场景函数：

```js
//变量
scene.children.length; //场景中物体的个数
 
//函数
scene.add(obj);	//向场景中添加物体
scene.remove(obj); //删除场景中的物体
scene.children();	//获取场景中的所有子对象
scene.getChildByName(name);	//通过名称获取场景中的物体对象
scene.traverse(function (e){});	//遍历场景中的每个物体，对物体进行操作
```

**相机(camera)**

相机决定了场景中哪个角度的景色会被渲染出来

```js
//参数一：视野角fov,视野角越大，场景中的物体越小，视野角越小，场景中的物体越大,该值以度为单位;人差不多有180度的视角，但计算机显示器一般会选择一块儿较小的区域。对于游戏来说，大多数情况下会用60-90度左右的视角。推荐默认值：45。
//参数二：纵横比aspect,3d物体的宽高比例,一般会使用整个窗口作为输出界面。推荐默认值：window.innerWidth/window.innerHeight。
//参数三：相机离物体最近的距离near,相机开始渲染场景的地方，通常会设一个很小的值，从而可以看到所有物体。推荐默认值：0.1。
//参数四：相机离物体最远的距离far,相机结束渲染场景的地方，如果值太低，场景中的一部分可能不会被渲染；如果值太高，某些情况下会影响渲染效率。推荐默认值：1000。
var camera = new THREE.PerspectiveCamera(fov,aspect,near,far);
```

![](img\frustum-3d.svg)

**渲染器(Renderer)**

渲染器决定了渲染的结果应该画在元素的什么元素上面，并且以怎样的方式来绘制。

```
var renderer = new THREE.WebGLRenderer();
renderer.setSize( window.innerWidth, window.innerHeight );
```

![](img\scene-down.svg)

### 4.入门案例

```js
//可以看到一个转动的正方体，调整参数查看变化，你可以随意调整canvas的大小
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <style>
    html,
    body {
      margin: 0;
      padding: 0;
      height: 100%;
    }

    #c {
      width: 300px;
      height: 300px;
      display: block;
    }
  </style>
</head>

<body>
  <canvas id="c"></canvas>
  <script src="./three.js"></script>
  <script>
    'use strict';
    function main() {
      //1.获取canvas标签
      const canvas = document.querySelector('#c');
      //2.将画布传递给three.js用来渲染
      const renderer = new THREE.WebGLRenderer({
        canvas
      });
      //3.设置相机
      const fov = 75;
      const aspect = 2;
      const near = 0.1;
      const far = 1000;
      const camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
      //将相机沿着Z轴移动5个单位的距离；
      camera.position.z = 2;

      //4.设置一个场景
      const scene = new THREE.Scene();
      scene.background = new THREE.Color(0xAAAAAA);
      //为场景增加一个光源
      {
        //光的颜色
        const color = 0xffffff;
        //光的强度
        const intensity = 1;
        //
        const light = new THREE.DirectionalLight(color, intensity);
        //光的位置
        light.position.set(2, 2, 2);
        //允许投射阴影
        light.castShadow = true;
        //将光追加到场景中
        scene.add(light);
      }

      //5.创建物体，这里创建一个正方体
      const boxWidth = 1;
      const boxHeight = 1;
      const boxDepth = 1;
      const boxgeometry = new THREE.BoxGeometry(boxWidth, boxHeight, boxDepth);

      //6.创建物体的材质,要选中感光材质的
      const material = new THREE.MeshLambertMaterial({
        color: 0x44aa88,
      })

      //7.创建一个网格
      const cube = new THREE.Mesh(boxgeometry, material);
      cube.position.set(0,0,0);
      //允许接收阴影
      cube.receiveShadow = true;
      //8.将网格追加到场景中
      scene.add(cube);

      //9.将场景渲染到相机中
      //将画布的像素数与css大小设置为相同
      function resizeRendererToDisplaySize(renderer) {
        const canvas = renderer.domElement;
        //设备像素
        const pixelRatio = window.devicePixelRatio;
        const width = canvas.clientWidth * pixelRatio | 0;
        const height = canvas.clientHeight * pixelRatio | 0;
        const needResize = canvas.width !== width || canvas.height !== height;
        if (needResize) {
          renderer.setSize(width, height, false);
        }
        return needResize;
      }

      function render(time) {
        time *= 0.001;
        if(resizeRendererToDisplaySize(renderer)){
          //更新渲染循环，根据窗口的尺寸大小来调节画布
          const canvas = renderer.domElement;
          camera.aspect = canvas.clientWidth / canvas.clientHeight;
          camera.updateProjectionMatrix();
        }

        cube.rotation.x = time;
        cube.rotation.y = time;

        renderer.render(scene, camera);

        requestAnimationFrame(render);
      }
      requestAnimationFrame(render);
    }
    main();
  </script>
</body>

</html>
```

### 5. 光源Light

在设置了场景后可以为场景增加光源，是物体显示更加动态。

前提，物体的材质要用感光性质的，详情看材质Material，对于光可以设置允许产生阴影，对于物体允许接收阴影。

**常用属性**

- color:光的颜色
- intensity ：光的强度

**常用的有4光源**

1. AmbientLight：环境光

   ```js
   const ambientLight = new THREE.AmbientLight(color, intensity);
   ```

2. PointLight:点光源

   ```js
   const light = new THREE.PointLight(color,intensity);
   ```

3. SpotLight:聚光灯光源

   ```js
   const light = new THREE.SpotLight(color, intensity);
   ```

4. DirectionLight:方向光

   ```js
   const light = new THREE.DirectionalLight(color,intensity);
   ```

除了环境光外其余都能产生阴影效果，环境光不能。

**光源的位置**

```js
//光位置，不能设置与相机位置相同，不然看不到阴影
light.position.set(-50, 50, 50);
```

**允许投射阴影**

```js
//允许投射阴影
light.castShadow = true;
```

### 6.材质

1. MeshBasicMaterial 基础网格材质（常用）

基础材质，可以设置几何体的颜色、透明度、纹理贴图等常见的外观属性，基础材质对场景中的光照没有颜色变化反应。

2. MeshStandardMaterial新型标准化材质

能对矩形光源生效 ，可以制作出金属或者玻璃效果 

3. MeshPhongMaterial金属发亮的物体（常用）

支持镜面反射光照模型，给几何体添加局部高光效果；

  4.THREE.MeshLambertMaterial暗淡不发光

除了支持基础材质的属性外，还支持漫反射光照模型，可以设置反射率、折射率等物理光学参数，材质颜色明暗受光照强弱方向影响。

5. THREE.ShaderMaterial着色器材质

6. MeshDepthMaterial是深度材质

颜色的计算收到深度值的影响

7. MeshNormalMaterial是法向量材质

物体的表面不同的位置有不同的法线方向，从几何体对象的角度看， 几何体顶点相关数据一般都有顶点法向量数据，这些数据会被用于顶点的颜色计算。 

### 7.不规则图形的绘制

案例：

```js
//初始化几何形状
var footers = new THREE.Geometry() //点的集合
//创建点
//back
var d0 = new THREE.Vector3(2, 0, 0) //1
var d1 = new THREE.Vector3(2, 1, 0)
var d2 = new THREE.Vector3(1, 2, 0)
var d3 = new THREE.Vector3(-1, 2, 0)
var d4 = new THREE.Vector3(-2, 1, 0)
var d5 = new THREE.Vector3(-2, 0, 0)
//front
var d6 = new THREE.Vector3(2, 0, 1) //1
var d7 = new THREE.Vector3(2, 1, 1)
var d8 = new THREE.Vector3(1, 2, 1)
var d9 = new THREE.Vector3(-1, 2, 1)
var d10 = new THREE.Vector3(-2, 1, 1)
var d11 = new THREE.Vector3(-2, 0, 1)
//将点添加进集合
footers.vertices.push(d0, d1, d2, d3, d4, d5, d6, d7, d8, d9, d10, d11)
//连接点成面
footers.faces.push(
//back
new THREE.Face3(0, 1, 2),
new THREE.Face3(0, 2, 3),
new THREE.Face3(0, 3, 4),
new THREE.Face3(0, 4, 5),
//front
new THREE.Face3(6, 7, 8),
new THREE.Face3(6, 8, 9),
new THREE.Face3(6, 9, 10),
new THREE.Face3(6, 10, 11),
//right
new THREE.Face3(0, 1, 7),
new THREE.Face3(0, 7, 6),
//right-top
new THREE.Face3(1, 2, 8),
new THREE.Face3(1, 8, 7),
//top
new THREE.Face3(2, 3, 9),
new THREE.Face3(2, 9, 8),
//left-top
new THREE.Face3(3, 4, 10),
new THREE.Face3(3, 10, 9),
//left
new THREE.Face3(4, 5, 11),
new THREE.Face3(4, 11, 10),
//bottom
new THREE.Face3(0, 5, 11),
new THREE.Face3(0, 11, 6),
)
var meshers = new THREE.MeshBasicMaterial ({
color: 0x79797C
})
```

![](img\不规则图像.png)

### 8. 阴影的产生

1. 首先,允许渲染器渲染阴影

```js
renderer.shadowMap.enabled = true;
renderer.shadowMapType = THREE.PCFSoftShadowMap;//有效减少阴影马赛克
```

2. 创建物体允许发射接收阴影

```js
box.castShadow = true
```

3. 创建平面接收阴影

```js
plane.receiveShadow = true; 
```

4. 选择可以产生阴影的灯光

```
SpotLight,DirectionalLight,
```

![](img\shadow.png)

### 9.加载3D模型

1. 基本界面搭建好
2. https://www.blendswap.com/blends/index下载你想要的.bend文件的3D模型
3. 导出文件.obj形式，并导出模型的纹理
4. 需要引用的js文件

```js
<script src="/node_modules/three/build/three.js"></script>
<script src="/public/OrbitControls.js"></script>
<script src="/public/LoaderSupport.js"></script>
<script src="/public/OBJLoader2.js"></script>
<script src="/public/MTLLoader.js"></script>
```

5. 详细代码

```js
{
    //实例化一个加载器
    const objLoader = new THREE.OBJLoader2();
    objLoader.loadMtl('/windMill/windmill_001.mtl', null, (materials) => {
    objLoader.setMaterials(materials);
    //材质双面显示
    materials.side = THREE.DoubleSide;
    objLoader.load('/windMill/windmill_001.obj', (event) => {
        const root = event.detail.loaderRootNode;
        scene.add(root);
    })
  })
}
```

6. https://threejsfundamentals.org/threejs/lessons/threejs-load-obj.html详细教程（看官网比较好）

![](img\风车.png)