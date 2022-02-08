<template>
  <div>
    <div class="select_box">
      <el-select v-model="currentTabComponent" placeholder="请选择">
        <el-option
          v-for="item in tabComponentList"
          :key="item.value"
          :label="item.label"
          :value="item.value">
        </el-option>
      </el-select>
    </div>
    <component v-bind:is="currentTabComponent"></component>
  </div>
</template>

<script>
// Threejs，挂载到window上，只引入一次
import * as THREE from "three";
window.THREE = THREE;

import InitScene from "@/components/01Scene/initScene.vue";
import GeometryBox from "@/components/02Geometry/GeometryBox.vue";
import MaterialBox from "@/components/03Material/MaterialBox.vue";
import GeometryObject from "@/components/04GeometryObject/GeometryObject.vue";
import LightObject from "@/components/05Light/light.vue";

export default {
  components: {
    InitScene,
    GeometryBox,
    MaterialBox,
    GeometryObject,
    LightObject,
  },
  data() {
    return {
      currentTabComponent: 'LightObject',
      tabComponentList: [{
        label: '初始化threejs',
        value: 'InitScene'
      }, {
        label: '几何体',
        value: 'GeometryBox'
      }, {
        label: '材质',
        value: 'MaterialBox'
      }, {
        label: '模型对象',
        value: 'GeometryObject'
      }, {
        label: '光源对象',
        value: 'LightObject'
      }]
    };
  },
};
</script>

<style lang='scss' scoped>
.select_box {
  position: fixed;
  top: 30px;
  left: 30px;
  z-index: 2;
}
</style>