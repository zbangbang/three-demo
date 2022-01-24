<template>
  <div id="ThreeContainer"></div>
</template>

<script>
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
let scene, camera, controls, renderer, animationID;
let point;
export default {
  data() {
    return {
      // 内容区域宽高
      width: 0,
      height: 0,
    };
  },
  mounted() {
    const ThreeContainer = document.getElementById("ThreeContainer");
    this.width = ThreeContainer.clientWidth;
    this.height = ThreeContainer.clientHeight;
    this.initScene();
    this.animate();
  },
  beforeDestroy() {
    this.clearAll();
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
      const point = new THREE.PointLight(0xffffff, 1, 100);
      point.position.set(50, 50, 50);
      scene.add(point);

      const ambient = new THREE.AmbientLight(0x444444);
      scene.add(ambient);
    },

    // 绘制几何体
    initObject() {
      // 百万点
      // const vertices = [];
      // for(let i = 0; i < 1000000; i++) {
      //   const x = THREE.MathUtils.randFloatSpread(2000)
      //   const y = THREE.MathUtils.randFloatSpread(2000)
      //   const z = THREE.MathUtils.randFloatSpread(2000)

      //   vertices.push(x, y, z)
      // }
      // const geometry = new THREE.BufferGeometry()
      // geometry.setAttribute('position', new THREE.Float32BufferAttribute(vertices, 3))
      // const material = new THREE.PointsMaterial({
      //   color: 0x0000ff,
      //   size: 3
      // })
      // point = new THREE.Points(geometry, material)

      // 线材质
      const points = []
      points.push(new THREE.Vector3( -100, 0, 0 ))
      points.push(new THREE.Vector3( 0, 100, 0 ))
      points.push(new THREE.Vector3( 0, 0, 100 ))
      const geometry = new THREE.BufferGeometry().setFromPoints(points);
      const material = new THREE.LineDashedMaterial({
        color: 0x0000ff,
        dashSize: 2, //显示线段的大小。默认为3。
        gapSize: 5, //间隙的大小。默认为1
      });
      const line = new THREE.Line(geometry, material);
      line.computeLineDistances();

      scene.add(line);
    },

    // render
    render() {
      // const time = performance.now() * 0.001;
      // point.scale.set(Math.sin(time), Math.sin(time), Math.sin(time) * 2)

      renderer.render(scene, camera);
    },
    // 更新渲染
    animate() {
      this.render();
      animationID = requestAnimationFrame(this.animate);
    },

    // 清除场景
    clearAll() {
      if (!scene) return;
      scene.clear();
      scene = null;
      cancelAnimationFrame(animationID);
      renderer.dispose();
      renderer.forceContextLoss();
      renderer.content = null;
      renderer.domElement = null;
      renderer = null;
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