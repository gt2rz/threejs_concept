<template>
  <h1>This is a model - ThreeJs</h1>
  <div ref="target"></div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import * as THREE from 'three'
import { OrbitControls } from 'three/addons/controls/OrbitControls.js'
import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js'

const target = ref()

const scene = new THREE.Scene()
scene.background = new THREE.Color(0xffffff)

const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 1, 500)
camera.lookAt(new THREE.Vector3())
camera.position.set(0, 0, 50)

const renderer = new THREE.WebGLRenderer()
renderer.outputColorSpace = THREE.SRGBColorSpace
renderer.setSize(window.innerWidth / 2, window.innerHeight / 2)

const directionalLight = new THREE.DirectionalLight(0xffffff, 1)
directionalLight.position.set(0, 20, 10)
scene.add(directionalLight)

const ambientLight = new THREE.AmbientLight(0xffffff, 0.9) // Soft white light
scene.add(ambientLight)

const render = () => {
  renderer.render(scene, camera)
}

const loadHangar = () => {
  const loader = new GLTFLoader()
  loader.load(
    'models/hangar.glb',
    function (gltf) {
      // Calcular el centro del modelo
      const box = new THREE.Box3().setFromObject(gltf.scene)
      const center = box.getCenter(new THREE.Vector3())

      // Ajustar la posición del modelo para centrarlo
      gltf.scene.position.x += gltf.scene.position.x - center.x
      gltf.scene.position.y += gltf.scene.position.y - center.y
      gltf.scene.position.z += gltf.scene.position.z - center.z

      scene.add(gltf.scene)

      render()
    },
    function (event: ProgressEvent) {
      console.log((event.loaded / event.total) * 100 + '% loaded')
    },
    function (error: unknown) {
      console.error(error)
    }
  )
}

const controls = new OrbitControls(camera, renderer.domElement)
// controls.target.set(0, 0, 0)
controls.update()
controls.addEventListener('change', render)

// const  onWindowResize = () => {
//   camera.aspect = window.innerWidth / window.innerHeight;
//   camera.updateProjectionMatrix();
//   renderer.setSize( window.innerWidth /2, window.innerHeight/2 );
//   render();
// }

// window.addEventListener( 'resize', onWindowResize );

onMounted(() => {
  target.value.appendChild(renderer.domElement)
  loadHangar()
})
</script>

<style scoped></style>
