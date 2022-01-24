<template>
  <div id="ThreeContainer"></div>
</template>

<script>
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
let scene,camera,controls,renderer;
export default {
  data() {
    return {
      // 内容区域宽高
      width: 0,
      height: 0,
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
      const point = new THREE.PointLight(0xffffff, 1, 100)
      point.position.set(50, 50, 50)
      scene.add(point)

      const ambient=new THREE.AmbientLight(0x444444);
      scene.add(ambient);
    },

    // 绘制几何体
    initObject() {
      let box = new THREE.BoxGeometry(30, 30, 30)
      let material = new THREE.MeshLambertMaterial({
        color: 0xff0000,
        opacity: 0.7,
        transparent: true,
        wireframe: true
      })
      let boxMesh = new THREE.Mesh(box, material)
      scene.add(boxMesh)

      // BufferGeometry添加
      const bufferGeo = new THREE.BufferGeometry()
      const vertices = new Float32Array([
        0, 0, 0,
        50, 0, 0,
        0, 100, 0,
        // 0, 0, 0, // 两个重复点
        // 50, 0, 0,
        0, 0, 100,
      ])
      const attribute = new THREE.BufferAttribute(vertices, 3)
      bufferGeo.setAttribute('position', attribute)

      // // 顶点颜色缓冲区，需要开启vertexColors属性，开启之后先使用bufferGeo.attributes.color
      // const colors = new Float32Array([
      //   1, 0, 0,
      //   0, 1, 0,
      //   0, 0, 1,

      //   1, 1, 0,
      //   0, 1, 1,
      //   1, 0, 1,
      // ])
      // bufferGeo.setAttribute('color', new THREE.BufferAttribute(colors, 3))

      // 法向量缓冲区
      const normals = new Float32Array([
        0, 0, 1,
        0, 0, 1,
        0, 0, 1,

        // 0, 1, 0,
        // 0, 1, 0,
        0, 1, 0,
      ])
      bufferGeo.setAttribute('normal', new THREE.BufferAttribute(normals, 3))

      // 顶点索引，减少重复点，提高利用率
      const indexs = new Uint16Array([
        0, 1, 2,
        0, 1, 3,
      ])
      bufferGeo.setIndex(new THREE.BufferAttribute(indexs, 1))

      const bufferMaterial = new THREE.MeshLambertMaterial({
        color: 0x00ffff, //三角面颜色
        // vertexColors: true, // 开启顶点颜色缓冲区
        side: THREE.DoubleSide, //两面可见
      })

      // 改变顶点索引
      const indexs1 = new Uint16Array([
        0, 1, 2,
        0, 2, 3,
      ])
      bufferGeo.index = new THREE.BufferAttribute(indexs1, 1)

      // bufferGeo.scale(0.5, 1.5, 2)
      // console.log('bufferGeo', bufferGeo);
      // console.log('bufferGeo.attributes.position', bufferGeo.attributes.position);
      // console.log('bufferGeo.index', bufferGeo.index);

      const bufferMesh = new THREE.Mesh(bufferGeo, bufferMaterial)
      bufferMesh.scale.set(0.5, 1.5, 2)
      console.log('bufferGeo', bufferGeo);
      console.log('bufferGeo.attributes.position', bufferGeo.attributes.position);
      console.log('bufferGeo.index', bufferGeo.index);

      scene.add(bufferMesh)
    },

    // render
    render() {
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