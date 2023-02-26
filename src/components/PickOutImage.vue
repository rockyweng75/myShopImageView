<script lang="ts">
  import { ref, onMounted } from 'vue'
  // import like from '../assets/love-heart-valentine-romance-wedding-romantic-like-2-svgrepo-com.svg'
  // import dislike from '../assets/dislike-svgrepo-com.svg'

  export default {
    name: 'PickOutImage',
    inheritAttrs: false,
    props:{
      src: String,
      style: {
        type: [String, Object],
        require: false
      },
      class: {
        type: [String, Object],
        require: false
      },
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
      const img = ref(null)
      const svg = ref(null)

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
            // left
            if(startX.value - moveX.value > 0){
              status.value = props.custom.toLocaleLowerCase() === 'right'? 'dislike': 'like';
              emit('next', status.value);
            }
            // right
            if(startX.value - moveX.value < 0){
              status.value = props.custom.toLocaleLowerCase() === 'right'? 'like': 'dislike';
              emit('next', status.value);
            }
          }
        }
      }

      onMounted(()=>{

        let element = img.value as unknown as HTMLDivElement;

        let likeElement = document.querySelector<HTMLImageElement>('#like')
        if(props.custom.toLocaleLowerCase() === 'right'){
          likeElement!.style.right = '0'
        } else {
          likeElement!.style.left = '0'
        }

        let dislikeElement = document.querySelector<HTMLImageElement>('#dislike')
        if(props.custom.toLocaleLowerCase() === 'right'){
          dislikeElement!.style.left = '0'
        } else {
          dislikeElement!.style.right = '0'
        }

        if(element){
          element.onmousedown = (e: MouseEvent) =>{
            e.preventDefault();
            e.stopPropagation();
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
        img,
        svg
      }
    }
  }

</script>

<template>
  <div class="img-container" :style="style" :class="class">
    <img ref="img" :src="src"/>
    <img id="like" src="../assets/love-heart-valentine-romance-wedding-romantic-like-2-svgrepo-com.svg"/>
    <img id="dislike" src="../assets/dislike-svgrepo-com.svg"/>
    <img id="right-arrow" src="../assets/arrow-small-right-svgrepo-com.svg"/>
    <img id="left-arrow" src="../assets/arrow-small-left-svgrepo-com.svg"/>
  </div>
</template>

<style scoped>
.img-container {
  position: relative;
  min-width: 200px;
}

.img-container > img {
  width: 100%;
  height: 100%;
  z-index: 99;
}

.svg-container{
  z-index: -1;
  position: absolute;
}

@keyframes Move2Right {
    from { right: 10%; }
    to { right: 12%; }
}

@keyframes Move2Left {
    from { left: 10%; }
    to { left: 12%; }
}

#right-arrow {
  position: absolute;
  opacity: 0.2;
  z-index: 999;
  top: 0;
  right: 10%;
  width: 60px;
  animation-name: Move2Right;
  animation-duration: 1s;
  animation-iteration-count: infinite;
}

#left-arrow {
  position: absolute;
  opacity: 0.2;
  z-index: 999;
  top: 0;
  left: 10%;
  width: 60px;
  animation-name: Move2Left;
  animation-duration: 1s;
  animation-iteration-count: infinite;
}



#like {
  position: absolute;
  opacity: 0.4;
  z-index: 999;
  top: 0;
  width: 60px;

}

#dislike {
  position: absolute;
  opacity: 0.4;
  z-index: 999;
  top: 0;
  width: 60px;
}

</style>
