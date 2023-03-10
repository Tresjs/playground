<script setup lang="ts">
import { Ref, ref } from 'vue'

import { useTexture, useRenderLoop, TresInstance } from '@tresjs/core'
import { OrbitControls, Plane } from '@tresjs/cientos'

const pbrTexture = await useTexture({
  map: 'https://raw.githubusercontent.com/Tresjs/assets/main/textures/black-rock/Rock035_2K_Displacement.jpg',
  displacementMap:
    'https://raw.githubusercontent.com/Tresjs/assets/main/textures/black-rock/Rock035_2K_Displacement.jpg',
  roughnessMap: 'https://raw.githubusercontent.com/Tresjs/assets/main/textures/black-rock/Rock035_2K_Roughness.jpg',
  normalMap: 'https://raw.githubusercontent.com/Tresjs/assets/main/textures/black-rock/Rock035_2K_NormalGL.jpg',
  ambientOcclusion:
    'https://raw.githubusercontent.com/Tresjs/assets/main/textures/black-rock/Rock035_2K_AmbientOcclusion.jpg',
})

const blackRock = await useTexture({
  map: 'https://raw.githubusercontent.com/Tresjs/assets/main/textures/black-rock/Rock035_2K_Color.jpg',
  displacementMap:
    'https://raw.githubusercontent.com/Tresjs/assets/main/textures/black-rock/Rock035_2K_Displacement.jpg',
  roughnessMap: 'https://raw.githubusercontent.com/Tresjs/assets/main/textures/black-rock/Rock035_2K_Roughness.jpg',
  normalMap: 'https://raw.githubusercontent.com/Tresjs/assets/main/textures/black-rock/Rock035_2K_NormalGL.jpg',
  ambientOcclusion:
    'https://raw.githubusercontent.com/Tresjs/assets/main/textures/black-rock/Rock035_2K_AmbientOcclusion.jpg',
})

const sphereRef: Ref<TresInstance | null> = ref(null)

const { onLoop } = useRenderLoop()

onLoop(({ delta }) => {
  // I will run at every frame ~ 60FPS (depending of your monitor)
  if (sphereRef.value) {
    sphereRef.value.rotation.y += delta
  }
})
</script>

<template>
  <TresCanvas
    clear-color="#82DBC5"
    shadows
    alpha
    window-size
    power-preference="high-performance"
    preserve-drawing-buffer
  >
    <OrbitControls />
    <TresPerspectiveCamera :position="[11, 11, 11]" :fov="45" :aspect="1" :near="0.1" :far="1000" />
    <TresScene>
      <TresMesh ref="sphereRef" :position="[0, 4, 0]" :scale="1" cast-shadow>
        <TresSphereGeometry :args="[1, 500, 500]" />
        <TresMeshStandardMaterial v-bind="blackRock" displacement-scale="0.2" />
      </TresMesh>
      <Plane :args="[10, 10, 500, 500]">
        <TresMeshStandardMaterial v-bind="pbrTexture" displacement-scale="0.6" />
      </Plane>
      <TresDirectionalLight :position="[0, 2, 4]" :intensity="1" cast-shadow />
    </TresScene>
  </TresCanvas>
</template>
