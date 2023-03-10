<script setup lang="ts">
import { OrbitControls, Plane, useTweakPane } from '@tresjs/cientos'
import {
  BasicShadowMap,
  Color,
  CubeTextureLoader,
  MeshBasicMaterial,
  MeshLambertMaterial,
  MeshMatcapMaterial,
  MeshNormalMaterial,
  MeshPhongMaterial,
  MeshPhysicalMaterial,
  MeshStandardMaterial,
  MeshToonMaterial,
  NoToneMapping,
  sRGBEncoding,
} from 'three'
import { shallowReactive, shallowRef } from 'vue'

const { pane } = useTweakPane()
const state = shallowReactive({
  clearColor: '#4f4f4f',
  shadows: true,
  alpha: false,
  physicallyCorrectLights: true,
  shadowMapType: BasicShadowMap,
  outputEncoding: sRGBEncoding,
  toneMapping: NoToneMapping,
})

const cubeTextureLoader = new CubeTextureLoader()

const environmentMap = cubeTextureLoader.load([
  'https://raw.githubusercontent.com/Tresjs/assets/main/textures/environmentMap/px.jpg',
  'https://raw.githubusercontent.com/Tresjs/assets/main/textures/environmentMap/nx.jpg',
  'https://raw.githubusercontent.com/Tresjs/assets/main/textures/environmentMap/py.jpg',
  'https://raw.githubusercontent.com/Tresjs/assets/main/textures/environmentMap/ny.jpg',
  'https://raw.githubusercontent.com/Tresjs/assets/main/textures/environmentMap/pz.jpg',
  'https://raw.githubusercontent.com/Tresjs/assets/main/textures/environmentMap/nz.jpg',
])

const materialState = shallowReactive({
  color: '#008080',
  metalness: 0.5,
  wireframe: false,
})

const sphereRef = shallowRef()
pane
  .addInput(materialState, 'color', {
    label: 'Color',
  })
  .on('change', (ev: any) => {
    sphereRef.value.material.color = new Color(ev.value)
  })

pane
  .addInput(materialState, 'wireframe', {
    label: 'Wireframe',
  })
  .on('change', (ev: any) => {
    sphereRef.value.material.wireframe = ev.value
  })

const materialProps = ['metalness', 'roughness']

materialProps.forEach(element => {
  pane
    .addBlade({
      view: 'slider',
      label: element,
      min: 0,
      max: 1,
      value: 0.5,
    })
    .on('change', (ev: any) => {
      materialState[element] = ev.value
      sphereRef.value.material[element] = ev.value
    })
})

pane
  .addBlade({
    view: 'list',
    label: 'Materials',
    options: [
      { text: 'MeshBasicMaterial', value: MeshBasicMaterial },
      { text: 'MeshToonMaterial', value: MeshToonMaterial },
      /*    { text: 'MeshDepthMaterial', value: MeshDepthMaterial },
      { text: 'MeshDistanceMaterial', value: MeshDistanceMaterial }, */
      { text: 'MeshLambertMaterial', value: MeshLambertMaterial },
      { text: 'MeshMatcapMaterial', value: MeshMatcapMaterial },
      { text: 'MeshNormalMaterial', value: MeshNormalMaterial },
      { text: 'MeshPhongMaterial', value: MeshPhongMaterial },
      { text: 'MeshPhysicalMaterial', value: MeshPhysicalMaterial },
      { text: 'MeshStandardMaterial', value: MeshStandardMaterial },
    ],
    value: MeshToonMaterial,
  })
  .on('change', ev => {
    sphereRef.value.material = new ev.value(materialState)

    if (ev.value === MeshStandardMaterial || ev.value === MeshPhysicalMaterial) {
      sphereRef.value.material.envMap = environmentMap
    }
  })
</script>
<template>
  <TresCanvas v-bind="state">
    <TresPerspectiveCamera :position="[3, 3, 5]" :fov="45" :aspect="1" :near="0.1" :far="1000" />
    <OrbitControls />
    <TresScene>
      <TresMesh ref="sphereRef" :position="[0, 1, 0]" cast-shadow recieve-shadow>
        <TresSphereGeometry :args="[1, 32, 32]" />
        <TresMeshToonMaterial :color="materialState.color" />
      </TresMesh>
      <Plane :args="[10, 10]" recieve-shadow>
        <TresMeshToonMaterial color="#222" />
      </Plane>
      <TresDirectionalLight :position="[-3, 4, 4]" :intensity="2" cast-shadow />
      <TresDirectionalLight :position="[3, 4, 0]" :intensity="0.5" cast-shadow />

      <TresAmbientLight :intensity="1" />
    </TresScene>
  </TresCanvas>
</template>
