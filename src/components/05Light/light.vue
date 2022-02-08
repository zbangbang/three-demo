<template>
  <div id="ThreeContainer"></div>
</template>

<script>
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
let scene, camera, controls, renderer;
let boxMesh;
export default {
  data() {
    return {
      // 内容区域宽高
      width: 0,
      height: 0,

      // 前一时刻时间
      lastTime: Date.now(),
    };
  },
  mounted() {
    const ThreeContainer = document.getElementById("ThreeContainer");
    this.width = ThreeContainer.clientWidth;
    this.height = ThreeContainer.clientHeight;
    this.initScene();
    this.animate();
  },
  methods: {
    initScene() {
      scene = new THREE.Scene();

      // 坐标系辅助线
      const axesHelper = new THREE.AxesHelper(300);
      scene.add(axesHelper);

      // 添加灯光
      this.initLight();
      // 添加几何体
      this.initObject();

      // 相机
      const fov = 45;
      const near = 1;
      const far = 1000;
      camera = new THREE.PerspectiveCamera(
        fov,
        this.width / this.height,
        near,
        far
      );
      camera.position.set(500, 500, 500);
      camera.lookAt(0, 0, 0);

      // 渲染器
      renderer = new THREE.WebGLRenderer();
      renderer.setPixelRatio(window.devicePixelRatio);
      renderer.setSize(this.width, this.height);
      renderer.setClearColor(0xffffff, 1);
      renderer.shadowMap.enabled = true;
      ThreeContainer.appendChild(renderer.domElement);

      // 控制
      controls = new OrbitControls(camera, renderer.domElement);
      controls.maxPolarAngle = Math.PI * 0.495;
      controls.target.set(0, 0, 0);
      controls.minDistance = 100.0;
      controls.maxDistance = 1000.0;
      controls.update();
    },

    // initLight
    initLight() {
      // const point = new THREE.PointLight(0xffffff)
      // point.position.set(-150, 150, -150)
      // scene.add(point)

      // const ambient = new THREE.AmbientLight(0xff0000);
      // scene.add(ambient);

      const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
      directionalLight.position.set(50, 50, 50);
      scene.add(directionalLight);
      directionalLight.castShadow = true;
      // 设置计算阴影的区域，最好刚好紧密包围在对象周围
      // 计算阴影的区域过大：模糊  过小：看不到或显示不完整
      directionalLight.shadow.camera.near = 0.5;
      directionalLight.shadow.camera.far = 200;
      directionalLight.shadow.camera.left = -100;
      directionalLight.shadow.camera.right = 100;
      directionalLight.shadow.camera.top = 100;
      directionalLight.shadow.camera.bottom = -100;
      // directionalLight.shadow.mapSize.set(1024,1024)

      // // 聚光光源
      // const spotLight = new THREE.SpotLight(0xffffff);
      // // 设置聚光光源位置
      // spotLight.position.set(200, 300, 200);
      // // // 聚光灯光源指向网格模型mesh2
      // // spotLight.target = mesh2;
      // // 设置聚光光源发散角度
      // spotLight.angle = Math.PI / 18;
      // scene.add(spotLight); //光对象添加到scene场景中
    },

    // 绘制几何体
    initObject() {
      let boxGeometry = new THREE.BoxGeometry(100, 100, 100);
      let boxMaterial = new THREE.MeshLambertMaterial({
        color: 0xffffff,
      });
      const box = new THREE.Mesh(boxGeometry, boxMaterial);
      scene.add(box);
      box.castShadow = true;

      //创建一个平面几何体作为投影面
      const planeGeometry = new THREE.PlaneGeometry(300, 200);
      const planeMaterial = new THREE.MeshLambertMaterial({
        color: 0x666666,
      });
      // 平面网格模型作为投影面
      const planeMesh = new THREE.Mesh(planeGeometry, planeMaterial);
      scene.add(planeMesh); //网格模型添加到场景中
      planeMesh.rotateX(-Math.PI / 2);
      planeMesh.position.y = -50; //设置网格模型y坐标
      planeMesh.receiveShadow = true;
    },

    // render
    render() {
      renderer.render(scene, camera);
    },
    // 更新渲染
    animate() {
      this.render();
      requestAnimationFrame(this.animate);
    },
  },
};
</script>

<style lang='scss' scoped>
#ThreeContainer {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
}
</style>