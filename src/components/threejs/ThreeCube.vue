<template>
  <h1>This is a cube - ThreeJs</h1>
  <div ref="target"></div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import * as THREE from 'three'

const target = ref()

const scene = new THREE.Scene()
const camera = new THREE.PerspectiveCamera(50, window.innerWidth / window.innerHeight, 0.1, 1000)

const renderer = new THREE.WebGLRenderer()
if (!renderer) {
  throw new Error('WebGLRenderer not supported')
}
renderer.setSize(window.innerWidth / 2, window.innerHeight / 2)

const geometry = new THREE.BoxGeometry(1, 1, 1)
const material = new THREE.MeshBasicMaterial({ color: 0xff0000 })
const cube = new THREE.Mesh(geometry, material)
scene.add(cube)

camera.position.z = 5

function animate() {
  renderer.setAnimationLoop(animate)

  cube.rotation.x += 0.01
  cube.rotation.y += 0.03
  cube.rotation.z += 0.02

  renderer.render(scene, camera)
}

onMounted(() => {
  target.value.appendChild(renderer.domElement)
  animate()
})
</script>

<style scoped></style>
