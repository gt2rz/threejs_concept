<template>
  <h1>Warehouse</h1>
  <div id="visor">
    <div ref="target"></div>
     <div
    ref="tooltip"
    class="tooltip"
    v-if="tooltipVisible"
    :style="{ top: tooltipY + 'px', left: tooltipX + 'px' }"
  >
    {{ tooltipText }}
  </div>
    <section>
      <h2>Boxes</h2>
      <ul>
        <li v-for="box in boxes" :key="box">
          <pre>
            {{ box }}
          </pre>
        </li>
      </ul>
    </section>
  </div>

 
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import * as THREE from 'three'
import { OrbitControls } from 'three/addons/controls/OrbitControls.js'
import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js'

const props = defineProps({
  model: {
    type: String,
    required: true
  }
})

const boxes = ref([])

const raycaster = new THREE.Raycaster()
const mouse = new THREE.Vector2()

const tooltip = ref()
const tooltipVisible = ref(false)
const tooltipText = ref('')
const tooltipX = ref(0)
const tooltipY = ref(0)

const target = ref()

const scene = new THREE.Scene()
scene.background = new THREE.Color(0xffffff)

const camera = new THREE.PerspectiveCamera(20, window.innerWidth / window.innerHeight, 0.1, 2000)
camera.lookAt(new THREE.Vector3())
camera.position.set(200, 100, 100)

const renderer = new THREE.WebGLRenderer()
renderer.outputColorSpace = THREE.SRGBColorSpace
// renderer.setSize(window.innerWidth, window.innerHeight)
renderer.setSize(
  window.innerWidth - window.innerWidth * 0.2,
  window.innerHeight - window.innerHeight * 0.2
)
renderer.setPixelRatio(window.devicePixelRatio)

const directionalLight = new THREE.DirectionalLight(0xffffff, 1)
directionalLight.position.set(0, 20, 10)
scene.add(directionalLight)

const ambientLight = new THREE.AmbientLight(0xffffff, 1) // Soft white light
scene.add(ambientLight)

const render = () => {
  renderer.render(scene, camera)
}

const data = {
  boxes: [
    {
      id: 1,
      location: {rack: 1,module: 2, level: 3},
      name: 'Producto 1',
      description: 'Descripción del producto 1',
      tags: ['box'],
      products: [
        {
          name: 'Producto 1',
          description: 'Descripción del producto 1',
          quantity: 10
        }
      ]
    }
  ]
}

const loadHangar = () => {
  const loader = new GLTFLoader()
  loader.load(
    props.model,
    function (gltf) {
      // Calcular el centro del modelo
      // const box = new THREE.Box3().setFromObject(gltf.scene)
      // const center = box.getCenter(new THREE.Vector3())

      // // Ajustar la posición del modelo para centrarlo
      // gltf.scene.position.x += gltf.scene.position.x - center.x
      // gltf.scene.position.y += gltf.scene.position.y - center.y
      // gltf.scene.position.z += gltf.scene.position.z - center.z

      gltf.scene.traverse((child) => {
        if (child.isMesh) {
          // Aquí puedes aplicar cualquier operación a cada malla
          if (child?.userData?.name?.includes('boxes_')) {
            child.userData.tags = ['box']
            // console.log('Mesh found:', child)

            // Se ocultan todas las cajas
            child.visible = false

            if (child?.userData?.name?.includes('Standard_Euro_pallet_w_boxes_11849____1-2-3')) {
              child.visible = true
              // console.log('Mesh found:', child);
              console.log(JSON.stringify(child));
              child.material = new THREE.MeshStandardMaterial({ color: 0xffff00 })
            }

            // child.material = new THREE.MeshStandardMaterial({ color: 0xff0000 })

            // Se muestran solo las cajas 1 y 2
            // if (child?.id % 2 === 0) {
            //   child.visible = true

              // if (child?.id > 1000 && child?.id < 2000) {
              //   child.visible = true

              //   child.userData.metadata = {
              //     id: 1,
              //     location: {rack: 1,module: 2, level: 3},
              //     name: 'Producto 1',
              //     description: 'Descripción del producto 1',
              //     tags: ['box'],
              //     products: [
              //       {
              //         name: 'Producto 1',
              //         description: 'Descripción del producto 1',
              //         quantity: 10
              //       }
              //     ]
              //   }
              //   child.material = new THREE.MeshStandardMaterial({ color: 0xffff00 })
              // }
            // }
          }
          // Por ejemplo, cambiar el material de la malla
        }
      })

      scene.add(gltf.scene)

      render()
    },
    function (event: ProgressEvent) {
      // console.log((event.loaded / event.total) * 100 + '% loaded')
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

const onClick = (event) => {
  raycaster.setFromCamera(mouse, camera)
  const intersects = raycaster.intersectObjects(scene.children, true)

  if (intersects.length > 0) {
    const intersectedObject = intersects[0].object

    // console.log(intersects);    

    if (intersectedObject.userData.tags?.includes('box')) {
      intersectedObject.material = new THREE.MeshStandardMaterial({ color: 0x00ff00 })

      if (!boxes.value.includes(intersectedObject.name)) {
        boxes.value.push(intersectedObject.userData.metadata)
      }
      // console.log('Box clicked:', intersectedObject)
    }
  }
}

window.addEventListener('click', onClick, false)


const onMouseMove = (event) => {
  mouse.x = (event.clientX / window.innerWidth) * 2 - 1
  mouse.y = -(event.clientY / window.innerHeight) * 2 + 1

  raycaster.setFromCamera(mouse, camera)
  const intersects = raycaster.intersectObjects(scene.children, true)

  if (intersects.length > 0) {
  //  console.log('Intersected:', intersects[0].object);
   
    const intersectedObject = intersects[0].object
    if (intersectedObject.userData.tags?.includes('box')) {
      tooltipText.value = intersectedObject.name || 'Unnamed'
      tooltipX.value = event.clientX
      tooltipY.value = event.clientY
      tooltipVisible.value = true
      document.body.style.cursor = 'pointer' // Cambiar el cursor a pointer
    } else {
      tooltipVisible.value = false
      document.body.style.cursor = 'default' // Restaurar el cursor por defecto
    }
  } else {
    console.log('No intersected');
    
    tooltipVisible.value = false
    document.body.style.cursor = 'default' // Restaurar el cursor por defecto
  }
}

window.addEventListener('mousemove', onMouseMove, false)

onMounted(() => {
  target.value.appendChild(renderer.domElement)
  loadHangar()
})
</script>

<style scoped>
.tooltip {
  position: absolute;
  background-color: rgba(0, 0, 0, 0.7);
  color: white;
  padding: 5px;
  border-radius: 3px;
  pointer-events: none;
  z-index: 10;
}

#visor {
  display: flex;
}
</style>
