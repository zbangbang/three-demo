<template>
  <div id="ThreeContainer"></div>
</template>

<script>
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
let scene,camera,renderer,width,height,controls;
let meshArray = [];
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
      // controls.update();

      // // 渲染
      // renderer.render(scene, camera);
    },

    // 初始化光源
    initLight() {
      let point = new THREE.PointLight(0xff00ff);
      point.position.set(400, 200, 300);
      scene.add(point);

      const sphereSize = 100;
      const pointLightHelper = new THREE.PointLightHelper( point, sphereSize );
      scene.add( pointLightHelper );

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
      const mesh = new THREE.Mesh(geo, material);
      mesh.translateOnAxis(new THREE.Vector3(2, 1, 1.5), 150)
      mesh.type = 'sphere_1';
      meshArray.push(mesh);
      scene.add(mesh);

      const helper = new THREE.BoxHelper( mesh, 0xff0000 );
      scene.add( helper );
    },

    animate() {
      // 找到需要变化的mesh
      let mesh = meshArray.find(item => item.type === 'sphere_1')
      let t1 = new Date();
      let delta = t1 - this.t0;
      this.t0 = t1;
      renderer.render(scene, camera);
      requestAnimationFrame(this.animate);
      mesh.translateY(0.001 * delta);
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