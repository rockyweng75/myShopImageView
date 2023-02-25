<script lang="ts">
  import { ref, onMounted } from 'vue'
  export default {
    name: 'PickOutImage',
    inheritAttrs: false,
    props:{
      src: String,
      custom: { 
        type: String,
        require: false,
        default: 'right',
      },
    },
    emits:{
      next: Function
    },
    setup(props,{emit}){
      const dom = ref(null)
      const isDown = ref(false)
      const isMove = ref(false)

      const startX = ref(0)
      const startY = ref(0)
      const moveX = ref(0)
      const moveY = ref(0)
      const status = ref('none')

      const invoke = ()=>{
        if(isDown.value && isMove.value){
          if(Math.abs(startY.value - moveY.value) < Math.abs(startX.value - moveX.value)*1.5){
            if(startX.value - moveX.value > 0){
              status.value = props.custom.toLocaleLowerCase() === 'right'? 'dislike': 'like';
              emit('next', status.value);
            }
            
            if(startX.value - moveX.value < 0){
              status.value = props.custom.toLocaleLowerCase() === 'right'? 'like': 'dislike';
              emit('next', status.value);
            }
          }
        }
      }

      onMounted(()=>{
        let element = dom.value as unknown as HTMLImageElement;
        if(element){
          element.onmousedown = (e: MouseEventInit) =>{
            isDown.value = true;
            startX.value = e.clientX!;
            startY.value = e.clientY!;
          } 

          element.onmousemove = (e: MouseEvent) =>{
            e.preventDefault();
            e.stopPropagation();
            if(isDown.value){
              moveX.value = e.clientX!;
              moveY.value = e.clientY!;
              isMove.value = true;
            }
          } 

          element.onmouseup = (e: MouseEvent) =>{
            e.preventDefault();
            e.stopPropagation();

            invoke();
            isDown.value = false;
            startX.value = 0;
          } 

          element.onmouseout = (e: MouseEventInit) =>{
            invoke();
            isDown.value = false;
            startX.value = 0;
          } 
        }

      })
      return {
        dom
      }
    }
  }

</script>

<template>
  <img ref="dom" :src="src"/>
</template>

<style scoped>
img {
  width: 100%;
  height: 100%;
}
</style>
