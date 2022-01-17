<template>
  <div id="ThreeContainer"></div>
</template>

<script>
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
let scene,camera,renderer,width,height,controls;
export default {
  data() {
    return {

    }
  },
  mounted() {
    this.initScene();
    this.animate();
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
      renderer.setSize(width, height);
      renderer.setClearColor(0xffffff, 1);
      ThreeContainer.appendChild(renderer.domElement);

      // 控制
      controls = new OrbitControls(camera, renderer.domElement);
      controls.maxPolarAngle = Math.PI * 0.495;
      controls.target.set(0, 0, 0);
      controls.minDistance = 100.0;
      controls.maxDistance = 1000.0;
      controls.update();

      // // 渲染
      // renderer.render(scene, camera);
    },

    // 初始化光源
    initLight() {
      let point = new THREE.PointLight(0xf76832);
      point.position.set(400, 200, 300);
      scene.add(point);

      const sphereSize = 100;
      const pointLightHelper = new THREE.PointLightHelper( point, sphereSize );
      scene.add( pointLightHelper );
    },

    // 创建物体
    initObject() {
      let geo = new THREE.SphereGeometry(50, 50, 50);
      geo.translate(380, 180, 280)
      let material = new THREE.MeshNormalMaterial();

      const mesh = new THREE.Mesh(geo, material);
      scene.add(mesh);

      const helper = new THREE.BoxHelper( mesh, 0x000000 );
      scene.add( helper );
    },

    animate() {
      renderer.render(scene, camera);
      requestAnimationFrame(this.animate);
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