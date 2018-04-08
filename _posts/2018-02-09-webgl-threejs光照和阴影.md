---
layout: post
title: Threejs 光照和阴影
categories: webgl
tags: webgl threejs
---

> 在Threejs中，类似于我们现实世界，物体显示的颜色是由物体本身的颜色及光照的颜色相互叠加而得到的。

## threejs中光照有以下几种
### 环境光

（类似于漫反射，没有光照起点）

```
AmbientLight(color);
```
第一个参数表示光照颜色，默认为白色

```
var light = new THREE.AmbientLight(0xffffff);
scene.add(light);
```
环境光可以放在任意一个位置，不会衰减，不需要设置光强，各个点都一样，所以不用设置位置。

### 点光源

（类似于照明弹）

```
var light = new THREE.PointLight(0xffffff, 1, 0, 1);
light.position.set(2,2,2);
scene.add(light);
```

点光源是从一个点出发，向各个方向发射光线。

其第一个参数表示光颜色；第二个参数表示光照强度，范围为0～1；第3个参数表示光照距离，默认为0，表示无限远；第4个参数表示光照强度随着光照防线逐渐衰减的量，默认为1。

### 聚光灯

```
SpotLight( color, intensity, distance, angle, penumbra, decay );
```

第一个参数表示颜色，默认为白色；第二个参数表示光强，默认为1，范围0～1；第三个参数表示光照距离，默认为根据光源距离，线性衰减光源的最大距离；第4个参数表示Math.PI/2；第5个参数表示根据光照锥形计算的阴影的百分比；第6个参数表示光照强度随着光照防线逐渐衰减的量。

```
var light = new  THREE.SpotLight(0xffffff);
light.position.set(2,2,2);
scene.add(light);
```
聚光灯从开始位置以锥形的照射向目标位置发射光线，默认目标位置为原点。

### 方向光

（类似于太阳光）

```
DirectionalLight(color, intensity);
```

第一个参数表示颜色，默认为白色；第二个参数表示光强，默认为1，范围0～1.

```
var light = new THREE.DirectionalLight(0xffffff,0.8);
light.position.set(0,50,0);
scene.add(light);
```

方向光的创建接收2个参数，第一个表示光照颜色，第二个表示光照强度，光照强度取值范围为0～1，光照递增；

方向光是平行光，从其位置开始，向目标方向照射，不会随着距离而衰减；目标方向默认是原点，可以自定义，然而对于自定义目标位置，也需要加入到场景中才会生效；

```
scene.add(light.target);
```

另外，方向光的目标位置也可以是场景中的一个物体（这个物体需要有位置信息）；

因此，物体的位置不同，方向光作用于物体的面不同，看到的颜色也不同；

平行光可以设置光照强度，光照越强，显示的颜色越鲜艳，否则显示的颜色越暗淡；

### 半球光
半球光是一种位于场景上方，从天空逐渐递减到地面的一种光照，其可以为室外环境模拟更真实的光照效果；当模拟室外光照的时候，可以使用半球光作为基础光照，再添加方向光模拟太阳光。
```
HemisphereLight(skyColor, groundColor, intensity);
```

第一个参数表示天空颜色，默认白色；第二个参数表示地面颜色，默认为黑色；第三个参数表示光照强度，默认为1.

### 平面光
平面光是在一个平面上均匀的发射出一个矩形平面，平面光可以用来模拟明亮的窗户或者条形透明物体；

该光源threejs暂时还不支持，需要添加扩展库；
```
RectAreaLight(color, intensity, width, height);
```
第一个参数表示光照颜色，默认为白色；第二个参数表示光照强度，默认为1；第三个参数表示光照宽度，默认为10；第4个参数表示光照高度，默认为10.

### 阴影

在上述7种光照中，支持阴影的有：点光源，聚光灯，方向光。

由于阴影的计算是非常消耗性能的，因此阴影默认是不计算不显示的；需要显示阴影，有以下几个条件：

渲染器需要开启阴影计算
```
renderer.shadowMap.enabled = true;
```
光照支持阴影，光照开启阴影
```
light.castShadow = true;
```
需要计算阴影的物体开启阴影
```
cube.castShadow = true;
```
需要有接收阴影的物体
```
plane.receiveShadow = true;
```
例子：
```
var scene = new THREE.Scene();
var winW = window.innerWidth;
var winH = window.innerHeight;
var camera = new THREE.PerspectiveCamera(75,winW/winH,1,1000);
camera.position.z = -8;
camera.position.y = 3;
camera.lookAt({x:0,y:0,z:0});
camera.up.y=2;

var amlight = new THREE.AmbientLight(0x003300);
scene.add(amlight);

var splight = new  THREE.SpotLight(0xffffff);
splight.position.set(2,2,2);
scene.add(splight);

splight.castShadow = true;

var renderer = new THREE.WebGLRenderer({antialias: true});
renderer.setSize(winW,winH);
renderer.setClearColor(0xFFFFFF, 1.0);

renderer.shadowMap.enabled = true;

document.body.appendChild(renderer.domElement);

var geometry = new THREE.CubeGeometry(1,1,1);

var material = new THREE.MeshLambertMaterial({color: 0xff0000});
//表面粗糙的一种材质，可以起镜面反射作用；没有光照的时候，该材质反射黑色；当有光照的时候，可以将物体颜色反射出来

var cube = new THREE.Mesh(geometry,material);
cube.castShadow = true;
scene.add(cube);
cube.position.set(0,0,0);

//添加平面, 绘制阴影
var planeGeometry = new THREE.PlaneGeometry(100,60,10,10);
var planeMaterial = new THREE.MeshLambertMaterial({color:0xFFFFFF});
var plane = new THREE.Mesh(planeGeometry,planeMaterial);
plane.rotation.x = -0.5*Math.PI;
plane.position.y = -2;
plane.position.z = 0;
scene.add(plane);
plane.receiveShadow = true;

function render(){
	requestAnimationFrame(render);
	cube.rotation.y+=0.01;
	cube.rotation.z+=0.01;
	renderer.clear();
	renderer.render(scene,camera);
}
render();
```

总结：各种光照可以混合使用，结合物体的材质和纹理，创造丰富多彩的三维场景；在这些光照中，环境光一般作为一种基础光照，起到烘托场景，弱化阴影等效果，再配合其他光照的使用，满足更多需求。
