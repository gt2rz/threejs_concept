<template>
  <div class="three">
    <!-- <h1>This is theejs examples</h1> -->
    <div class="wrapper">
      <!-- <Cube />
      <Line />
      <Text /> -->
      <!-- <Hangar /> -->
      <Warehouse :model="'models/BodegaFunza_v4.glb'" />
    </div>
  </div>
</template>

<script setup>
// import Cube from '@/components/threejs/ThreeCube.vue'
// import Line from '@/components/threejs/ThreeLine.vue'
// import Text from '@/components/threejs/ThreeText.vue'

// import Hangar from '@/components/threejs/ThreeModel.vue'

import Warehouse from '@/components/threejs/Warehouse.vue'

</script>

<style>
/* @media (min-width: 1024px) {
  .three {
    min-height: 100vh;
    display: flex;
    align-items: center;
  }
} */
</style>
