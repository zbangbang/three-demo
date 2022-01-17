<template>
  <div>
    <init-scene></init-scene>
  </div>
</template>

<script>
// Threejs，挂载到window上，只引入一次
import * as THREE from "three";
window.THREE = THREE;

import InitScene from '@/components/01Scene/initScene.vue';

export default {
  components: {
    InitScene,
  }
};
</script>

<style>
</style>