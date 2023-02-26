<script setup lang="ts">
import PickOutImage from './components/PickOutImage.vue'
import vueSvg from './assets/vue.svg'
import { ref } from 'vue'
const imgSrc = ref("/vite.svg");
const next = (status: any) =>{
  if(imgSrc.value === vueSvg){
    imgSrc.value = "/vite.svg"
  } else {
    imgSrc.value = vueSvg
  }
}

const doubleClick = () =>{
  alert('doubleClick')
}
</script>

<template>
  <div class="container">
    <PickOutImage :src="imgSrc" v-on:next="next"  v-on:double-click="doubleClick" style="width: 300px" description="12345678">
    </PickOutImage>
  </div>
</template>

<style scoped>
  /* .container{
    min-width: 300px;
    height: 100%;
    position: relative;
  } */
</style>
