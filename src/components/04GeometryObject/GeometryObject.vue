<template>
  <div id="ThreeContainer"></div>
</template>

<script>
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
let scene,camera,controls,renderer;
let boxMesh;
export default {
  data() {
    return {
      // 内容区域宽高
      width: 0,
      height: 0,

      // 前一时刻时间
      lastTime: Date.now(),
    }
  },
  mounted() {
    const ThreeContainer = document.getElementById('ThreeContainer');
    this.width = ThreeContainer.clientWidth
    this.height = ThreeContainer.clientHeight
    this.initScene()
    this.animate()
  },
  methods: {
    initScene() {
      scene = new THREE.Scene()

      // 坐标系辅助线
      const axesHelper = new THREE.AxesHelper(300)
      scene.add(axesHelper)

      // 添加灯光
      this.initLight()
      // 添加几何体
      this.initObject()

      // 相机
      const fov = 45
      const near = 1
      const far = 1000
      camera = new THREE.PerspectiveCamera(fov, this.width/this.height, near, far)
      camera.position.set(500, 500, 500)
      camera.lookAt(0, 0, 0)

      // 渲染器
      renderer = new THREE.WebGLRenderer()
      renderer.setPixelRatio(window.devicePixelRatio)
      renderer.setSize(this.width, this.height)
      renderer.setClearColor(0xffffff, 1)
      ThreeContainer.appendChild(renderer.domElement)

      // 控制
      controls = new OrbitControls(camera, renderer.domElement)
      controls.maxPolarAngle = Math.PI * 0.495
      controls.target.set(0, 0, 0)
      controls.minDistance = 100.0
      controls.maxDistance = 1000.0
      controls.update()
    },

    // initLight
    initLight() {
      const point = new THREE.PointLight(0xffffff, 1, 200)
      point.position.set(100, 100, 100)
      scene.add(point)

      const ambient=new THREE.AmbientLight(0x444444);
      scene.add(ambient);
    },

    // 绘制几何体
    initObject() {
      // 点模型
      // const geometryObject = new THREE.BoxGeometry(100, 100, 100)
      // const material = new THREE.PointsMaterial({
      //   color: 0xff0000,
      //   size: 5
      // })
      // const points = new THREE.Points(geometryObject, material)
      // scene.add(points)

      // 线模型
      // const lineGeometry = new THREE.BoxGeometry(50, 50, 50)
      // const lineMaterial = new THREE.LineBasicMaterial({
      //   color: 0x00ff00,
      //   linewidth: 1,
      //   linecap: 'butt',
      //   linejoin: 'bevel'
      // })
      // const line = new THREE.Line(lineGeometry, lineMaterial)
      // scene.add(line)

      // 面模型
      const boxGeometry = new THREE.BoxGeometry(100, 100, 100)
      const boxMaterial = new THREE.MeshLambertMaterial({
        color: 0x0000ff,
        // wireframe: true,
      })
      boxMesh = new THREE.Mesh(boxGeometry, boxMaterial)
      boxMesh.scale.set(1.5, 2, 3)
      boxMesh.position.set(-50, 50, 0)
      const axisPos = new THREE.Vector3(1, 1, 1)
      axisPos.normalize()
      boxMesh.translateOnAxis(axisPos, 10)
      // boxMesh.rotateOnAxis(axisPos, -Math.PI / 6)
      scene.add(boxMesh)
      console.log(boxMesh.material.wireframe);
    },

    // render
    render() {
      /* -----旋转start----- */
      // const time = Date.now()
      // const axisPos = new THREE.Vector3(1, 1, 1)
      // axisPos.normalize()
      // if (time - this.lastTime > 10) {
      //   this.lastTime = time
      //   boxMesh.rotateOnAxis(axisPos, -Math.PI / 6)
      // }
      /* -----旋转end----- */

      renderer.render(scene, camera)
    },
    // 更新渲染
    animate() {
      this.render()
      requestAnimationFrame(this.animate)
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
}
</style>