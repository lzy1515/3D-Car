<script setup>
import * as THREE from 'three'
import { onMounted, ref } from 'vue'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'
import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js'

let controls
const colors = ['#ffc03f','#32a2b4','#5c5837','#a9a9a9','#4a435d','#f54925','#3799fe','#1b4893','#1e1e1e']
const canvasDom = ref(null)
const scene = new THREE.Scene()
const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000)
camera.position.set(0, 2, 6)
const renderer = new THREE.WebGLRenderer({
  antialias: true,
  alpha: true
})
renderer.setSize(window.innerWidth, window.innerHeight)

const render = () => {
  renderer.render(scene, camera)
  controls && controls.update()
  requestAnimationFrame(render)
}

const bodyMaterial = new THREE.MeshPhysicalMaterial({
  color: '#32a2b4',
  metalness: 0,
  roughness: 0.1,
  clearcoatRoughness: 0
})

const changeColor = (color) => {
  bodyMaterial.color.set(color)
}

onMounted(() => {
  canvasDom.value.appendChild(renderer.domElement)
  renderer.setClearColor('#000')
  scene.background = new THREE.Color('#ccc')
  scene.environment = new THREE.Color('#ccc')
  render()

  const gridHelper = new THREE.GridHelper(10, 10)
  gridHelper.material.opacity = 0.2
  gridHelper.material.transparent = true
  scene.add(gridHelper)

  controls = new OrbitControls(camera, renderer.domElement)
  controls.update()

  const loader = new GLTFLoader()
  loader.load('../public/model/scene.gltf', function (gltf) {
    // console.log(gltf)
    const mi = gltf.scene
    mi.traverse(child => {
      if(child.isMesh){
        // console.log(child)
      }
      // Object_1,Object_18：车身
      // Object_20：车顶
      // Object_32：左前车门
      // Object_39：左后车门
      // Object_45：右前车门
      // Object_52：右后车门
      // Object_56：车轮
      if(child.isMesh && child.name.includes('Object_56')){
        // child.material = wheelsMaterial
      }
      if(child.isMesh && (['Object_18','Object_32','Object_39','Object_45','Object_52'].includes(child.name))){
        child.material = bodyMaterial
      }
    })
    scene.add(mi)
  })

  const light1 = new THREE.DirectionalLight(0xffffff, 1)
  light1.position.set(0, 0, 10)
  scene.add(light1)
  const light2 = new THREE.DirectionalLight(0xffffff, 1)
  light2.position.set(0, 0, -10)
  scene.add(light2)
  const light3 = new THREE.DirectionalLight(0xffffff, 1)
  light3.position.set(10, 0, 0)
  scene.add(light3)
  const light4 = new THREE.DirectionalLight(0xffffff, 1)
  light4.position.set(-10, 0, 0)
  scene.add(light4)
  const light5 = new THREE.DirectionalLight(0xffffff, 1)
  light5.position.set(0, 10, 0)
  scene.add(light5)
  const light6 = new THREE.DirectionalLight(0xffffff, 1)
  light5.position.set(5, 10, 0)
  scene.add(light6)
  const light7 = new THREE.DirectionalLight(0xffffff, 1)
  light5.position.set(0, 10, 5)
  scene.add(light7)
  const light8 = new THREE.DirectionalLight(0xffffff, 1)
  light5.position.set(0, 10, -0)
  scene.add(light8)
  const light9 = new THREE.DirectionalLight(0xffffff, 1)
  light5.position.set(-5, 10, 0)
  scene.add(light9)
})

</script>
<template>
  <div class="main">
    <div class="canvas-container" ref="canvasDom"></div>
  </div>
  <div class="content">
    <h1>选择汽车颜色</h1>
    <ul>
      <li v-for="(item,index) in colors" :key="index" :style="{background:item}" @click="changeColor(item)"></li>
    </ul>
  </div>
</template>

<style scoped>
.content{
  position: fixed;
  top: 10px;
  right: 10px;
}
ul{
  display: flex;
  padding: 0;
  list-style: none;
}
ul li{
  width: 40px;
  height: 40px;
  margin-left: 10px;
  border-radius: 50%;
  cursor: pointer;
}
</style>
