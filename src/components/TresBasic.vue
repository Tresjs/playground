<script setup lang="ts">
import { reactive } from 'vue'
import { BasicShadowMap, sRGBEncoding, NoToneMapping } from 'three'

import { OrbitControls } from '@tresjs/cientos'

const state = reactive({
  clearColor: '#82DBC5',
  shadows: true,
  alpha: false,
  physicallyCorrectLights: true,
  shadowMapType: BasicShadowMap,
  outputEncoding: sRGBEncoding,
  toneMapping: NoToneMapping,
})
</script>

<template>
  <TresCanvas v-bind="state">
    <OrbitControls />
    <TresPerspectiveCamera :position="[11, 11, 11]" :fov="45" :aspect="1" :near="0.1" :far="1000" />
    <TresScene>
      <TresMesh :position="[-2, 2, 0]" :rotation="[0, Math.PI, 0]" cast-shadow>
        <TresConeGeometry :args="[1, 1.5, 3]" />
        <TresMeshToonMaterial color="#82DBC5" />
      </TresMesh>
      <TresMesh :position="[0, 0, 0]" cast-shadow>
        <TresBoxGeometry :args="[1.5, 1.5, 1.5]" />
        <TresMeshToonMaterial color="#4F4F4F" />
      </TresMesh>
      <TresMesh :position="[2, -2, 0]" cast-shadow>
        <TresSphereGeometry />
        <TresMeshToonMaterial color="#FBB03B" />
      </TresMesh>
      <TresDirectionalLight :position="[0, 2, 4]" :intensity="2" cast-shadow />
    </TresScene>
  </TresCanvas>
</template>
