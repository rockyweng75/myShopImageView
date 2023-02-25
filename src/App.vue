<script setup lang="ts">
import PickOutImage from './components/PickOutImage.vue'
import vueSvg from './assets/vue.svg'
import { ref } from 'vue'
const imgSrc = ref("/vite.svg");
const next = (status: any) =>{
  console.log(status)
  if(imgSrc.value === vueSvg){
    imgSrc.value = "/vite.svg"
  } else {
    imgSrc.value = vueSvg
  }
}
</script>

<template>
  <div>
    <a href="https://vitejs.dev" target="_blank">
      <img src="/vite.svg" class="logo" alt="Vite logo" />
    </a>
    <a href="https://vuejs.org/" target="_blank">
      <img src="./assets/vue.svg" class="logo vue" alt="Vue logo" />
    </a>
  </div>
  <PickOutImage :src="imgSrc" v-on:next="next"/>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
