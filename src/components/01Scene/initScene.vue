<template>
  <div id="ThreeContainer"></div>
</template>

<script>
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
// threejs 场景相关对象
let scene,camera,renderer,width,height,controls,raycaster;
// 几何体相关对象
let boxMesh,pickGeometry;
let meshArray = [];
// 鼠标点
let mouse;
let requestAF;
export default {
  data() {
    return {
      t0: null,
    }
  },
  mounted() {
    this.t0 = new Date();
    this.initScene();
    this.animate();
  },
  beforeDestroy() {
    cancelAnimationFrame(requestAF)
    document.removeEventListener('click', this.onMouseClick)
  },
  methods: {
    initScene() {
      const ThreeContainer = document.getElementById('ThreeContainer');
      width = ThreeContainer.clientWidth;
      height = ThreeContainer.clientHeight;
      // scene
      scene = new THREE.Scene();

      // 坐标系辅助线
      const axesHelper = new THREE.AxesHelper(300);
      scene.add(axesHelper);

      // 初始化light
      this.initLight();

      // 创建cube
      this.initObject();

      // 相机
      const fov = 45;
      camera = new THREE.PerspectiveCamera(fov, width/height, 1, 1000);
      camera.position.set(600, 400, 500);
      camera.lookAt(0, 0, 0);

      // 渲染器对象
      renderer = new THREE.WebGLRenderer();
      renderer.setPixelRatio( window.devicePixelRatio );
      renderer.setSize(width, height);
      renderer.setClearColor(0xffffff, 1);
      ThreeContainer.appendChild(renderer.domElement);

      // 控制
      controls = new OrbitControls(camera, renderer.domElement);
      controls.maxPolarAngle = Math.PI * 0.495;
      controls.target.set(0, 0, 0);
      controls.minDistance = 100.0;
      controls.maxDistance = 1000.0;
      // controls.update();

      // 初始化射线检测
      raycaster = new THREE.Raycaster();
      mouse = new THREE.Vector2(-1, -1);

      document.addEventListener( 'click', this.onMouseClick );
    },

    // 初始化光源
    initLight() {
      let point = new THREE.PointLight(0xff00ff);
      point.position.set(0, 0, 0);
      scene.add(point);

      // const sphereSize = 100;
      // const pointLightHelper = new THREE.PointLightHelper( point, sphereSize );
      // scene.add( pointLightHelper );

      // 点光源2  位置和point关于原点对称
      let point2 = new THREE.PointLight(0xffffff);
      point2.position.set(400, 200, 300); //点光源位置
      scene.add(point2); //点光源添加到场景中

      //环境光
      let ambient=new THREE.AmbientLight(0x444444);
      scene.add(ambient);
    },

    // 创建物体
    initObject() {
      let geo = new THREE.SphereGeometry(50, 50, 50);
      let material = new THREE.MeshLambertMaterial({
        color: 0xff0000,
        opacity: 0.7,
        transparent: true,
        // wireframe: true
      });
      // let material = new THREE.MeshPhongMaterial({
      //   color:0x0000ff,
      //   specular:0x4488ee,
      //   shininess:12
      // });
      boxMesh = new THREE.Mesh(geo, material);
      boxMesh.translateOnAxis(new THREE.Vector3(2, 1, 1.5), 150)
      boxMesh.type = 'sphere_1';
      meshArray.push(boxMesh);
      scene.add(boxMesh);

      // 立方体网格模型
      let geometry1 = new THREE.BoxGeometry(100, 100, 100);
      let material1 = new THREE.MeshLambertMaterial({
        color: 0x0000ff
      }); //材质对象Material
      material1.opacity = 0.5
      material1.transparent = true
      let mesh1 = new THREE.Mesh(geometry1, material1); //网格模型对象Mesh
      scene.add(mesh1); //网格模型添加到场景中

      // 球体网格模型，镜面反射
      let geometry2 = new THREE.SphereGeometry(60, 40, 40);
      let material2 = new THREE.MeshPhongMaterial({
        color:0x0000ff,
        specular:0x4488ee,
        shininess:15
      });
      let mesh2 = new THREE.Mesh(geometry2, material2); //网格模型对象Mesh
      mesh2.translateY(120); //球体网格模型沿Y轴正方向平移120
      scene.add(mesh2);

      // 圆柱网格模型
      let geometry3 = new THREE.CylinderGeometry(50, 50, 100, 25);
      let material3 = new THREE.MeshLambertMaterial({
        color: 0xffff00,
        wireframe: true
      });
      let mesh3 = new THREE.Mesh(geometry3, material3); //网格模型对象Mesh
      // mesh3.translateX(120); //球体网格模型沿Y轴正方向平移120
      mesh3.position.set(120,0,0);//设置mesh3模型对象的xyz坐标为120,0,0
      scene.add(mesh3); //
    },

    // 鼠标点击事件
    onMouseClick(ev) {
      // 将 x 0~1 映射到 -1~1
      mouse.x = (ev.clientX / ThreeContainer.clientWidth) * 2 - 1;
      // 将 y 0~1 映射到 1~-1  canvas y轴是反向的
      mouse.y = -(ev.clientY / ThreeContainer.clientHeight) * 2 + 1;
    },

    render() {
      // 找到需要变化的mesh
      let mesh = meshArray.find(item => item.type === 'sphere_1')

      // 通过摄像机和鼠标位置更新射线
      raycaster.setFromCamera( mouse, camera );
      const intersects = raycaster.intersectObjects( scene.children, false );
      if (intersects.length > 0) {
        if (pickGeometry != intersects[0].object) {
          if (pickGeometry) pickGeometry.material.color.setHex(pickGeometry.currentColor)

          pickGeometry = intersects[0].object
          pickGeometry.currentColor = pickGeometry.material.color.getHex()
          pickGeometry.material.color.setHex(0xffffff)
        }
      } else {
        if (pickGeometry) pickGeometry.material.color.setHex(pickGeometry.currentColor)

        pickGeometry = null
      }

      // let t1 = new Date();
      // let delta = t1 - this.t0;
      // this.t0 = t1;
      // mesh.translateY(0.001 * delta);

      renderer.render(scene, camera);
    },
    animate() {
      this.render()
      requestAF = requestAnimationFrame(this.animate);
    }
  }
}
</script>

<style lang='scss' scoped>
#ThreeContainer {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
  // background: #ccc;
}
</style>