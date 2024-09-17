<template>
  <h1>This is a text - ThreeJs</h1>
  <div ref="target"></div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import * as THREE from 'three'
import { OrbitControls } from 'three/addons/controls/OrbitControls.js';
import { FontLoader } from 'three/addons/loaders/FontLoader.js';

const target = ref()

const scene = new THREE.Scene()
scene.background = new THREE.Color(0xf0f0f0)

const camera = new THREE.PerspectiveCamera(120, window.innerWidth / window.innerHeight, 1, 500)
camera.position.set(0, -400, 600)

const renderer = new THREE.WebGLRenderer( { antialias: true } );
// renderer.setPixelRatio( window.devicePixelRatio );
renderer.setSize( window.innerWidth/2, window.innerHeight/2 );

const render = () => {
  renderer.render(scene, camera)
}

const loadFont = () => {
  const loader = new FontLoader();
  loader.load( 'fonts/helvetiker_regular.typeface.json', function ( font: any ) {

    const color = 0x006699;

    const matDark = new THREE.LineBasicMaterial( {
      color: color,
      side: THREE.DoubleSide
    } );

    const matLite = new THREE.MeshBasicMaterial( {
      color: color,
      transparent: true,
      opacity: 0.4,
      side: THREE.DoubleSide
    } );

    const message = '   Three.js\nSimple text.';

    const shapes = font.generateShapes( message, 100 );

    const geometry = new THREE.ShapeGeometry( shapes );

    geometry.computeBoundingBox();
    const max = geometry?.boundingBox?.max.x || 0;
    const min = geometry?.boundingBox?.min.x || 0;
    const xMid = -( max - min );

    geometry.translate( xMid, 10, 30 );

    // make shape ( N.B. edge view not visible )

    const text = new THREE.Mesh( geometry, matLite );
    text.position.z = -150;
    scene.add( text );

    // make line shape ( N.B. edge view remains visible )

    const holeShapes = [];

    for ( let i = 0; i < shapes.length; i ++ ) {

      const shape = shapes[ i ];

      if ( shape.holes && shape.holes.length > 0 ) {

        for ( let j = 0; j < shape.holes.length; j ++ ) {

          const hole = shape.holes[ j ];
          holeShapes.push( hole );

        }

      }

    }

    shapes.push.apply( shapes, holeShapes );

    const lineText = new THREE.Object3D();

    for ( let i = 0; i < shapes.length; i ++ ) {

      const shape = shapes[ i ];

      const points = shape.getPoints();
      const geometry = new THREE.BufferGeometry().setFromPoints( points );

      geometry.translate( xMid, 0, 0 );

      const lineMesh = new THREE.Line( geometry, matDark );
      lineText.add( lineMesh );

    }

    scene.add( lineText );

    render();

  }, undefined, function ( error ) {
    console.error( 'An error happened while loading the font:', error );
  });
}

const controls = new OrbitControls( camera, renderer.domElement );
controls.target.set( 0, 0, 0 );
console.log(controls);

controls.update();
controls.addEventListener( 'change', render );


const  onWindowResize = () => {
  camera.aspect = window.innerWidth / window.innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize( window.innerWidth, window.innerHeight );
  render();
}

window.addEventListener( 'resize', onWindowResize );

onMounted(() => {
  target.value.appendChild(renderer.domElement)
  loadFont()
})
</script>

<style scoped></style>
