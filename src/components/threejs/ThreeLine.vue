<template>
  <h1>This is a Lines - ThreeJs</h1>
  <div ref="target"></div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import * as THREE from 'three'

const target = ref()

const scene = new THREE.Scene()
const camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 1, 500)
camera.position.set(0, 0, 100)
camera.lookAt(0, 0, 0)

scene.background = new THREE.Color(0xf0f0f0)

const renderer = new THREE.WebGLRenderer()
if (!renderer) {
  throw new Error('WebGLRenderer not supported')
}
renderer.setSize(window.innerWidth / 2, window.innerHeight / 2)

const material = new THREE.LineBasicMaterial({ color: 0x00ff00 })

const points = []
points.push(new THREE.Vector3(-10, 0, 0))
points.push(new THREE.Vector3(-5, 10, 0))
points.push(new THREE.Vector3(0, 5, 0))
points.push(new THREE.Vector3(5, 20, 0))
points.push(new THREE.Vector3(10, 0, 0))

const geometry = new THREE.BufferGeometry().setFromPoints(points)
const line = new THREE.Line(geometry, material)

scene.add(line)

const pointsLineTwo = []
pointsLineTwo.push(new THREE.Vector3(-10, 20, 0))
pointsLineTwo.push(new THREE.Vector3(-5, 12, 0))

const geometryLineTwo = new THREE.BufferGeometry().setFromPoints(pointsLineTwo)
const materialLineTwo = new THREE.LineBasicMaterial({ color: 0xff0000 })
const lineTwo = new THREE.Line(geometryLineTwo, materialLineTwo)
scene.add(lineTwo)

function animate() {
  renderer.setAnimationLoop(animate)

  line.rotation.y += 0.01
  // line.rotation.y += 0.03

  lineTwo.rotation.x -= 0.05

  renderer.render(scene, camera)
}

onMounted(() => {
  target.value.appendChild(renderer.domElement)
  // renderer.render(scene, camera)
  animate()
})
</script>

<style scoped></style>
