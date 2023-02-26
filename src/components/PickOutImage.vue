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
      description:{
        type: String,
        require: false,
      },
      firstTeaching:{
        type: Boolean,
        require: false,
        default: true
      }
    },
    emits:{
      next: Function,
      doubleClick: Function
    },
    setup(props,{emit}){
      const img = ref(null)
      const svg = ref(null)

      const isDown = ref(false)
      const downCount = ref(0)
      const clickTime = ref(0)

      const isMove = ref(false)

      const startX = ref(0)
      const startY = ref(0)
      const moveX = ref(0)
      const moveY = ref(0)
      const status = ref('none')

      const invokeMove = ()=>{
        if(isDown.value && isMove.value){
          if(Math.abs(startY.value - moveY.value) < Math.abs(startX.value - moveX.value)*1.5){
            if(Math.abs(startX.value - moveX.value) > 10){
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
      }

      const invokeDoubleClick = ()=>{
        emit('doubleClick');
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
            if(downCount.value > 0){
              if((Date.now() - clickTime.value) < 1000){
                downCount.value += 1;
              }
            } else {
              downCount.value += 1;
            }
            clickTime.value = Date.now()
            isDown.value = true;
            startX.value = e.clientX!;
            startY.value = e.clientY!;
          } 

          element.onmousemove = (e: MouseEventInit) =>{
            if(isDown.value){
              moveX.value = e.clientX!;
              moveY.value = e.clientY!;
              isMove.value = true;
              downCount.value = 0;
              clickTime.value = 0;
            } else {
              isDown.value = false;
            }
          } 

          element.onmouseup = (e: MouseEvent) =>{
            e.preventDefault();
            e.stopPropagation();
            if(downCount.value > 1 ){
              downCount.value = 0;
              clickTime.value = 0;
              invokeDoubleClick();
            } else {
              invokeMove();
            }
            isDown.value = false;
            startX.value = 0;
          } 

          element.onmouseout = (e: MouseEventInit) =>{
            invokeMove();
            isDown.value = false;
            isMove.value = false;
            startX.value = 0;
            downCount.value = 0;
            clickTime.value = 0;

          } 
        }

        let blankElement = document.querySelector<HTMLDivElement>('.blank-container')
        blankElement!.onclick = (e: Event)=>{
          blankElement!.style.display = "none";
        }
      })
      return {
        img,
        svg,
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
    <div class="desc-container" v-if="description">
      {{ description }}
    </div>
    <div v-if="firstTeaching" class="blank-container">
      <div class="left">{{custom.toLocaleLowerCase() === 'right' ? '不喜歡': '喜歡'}}向左滑</div>
      <div class="right">{{custom.toLocaleLowerCase() === 'right' ? '喜歡': '不喜歡'}}向右滑</div>
    </div>
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
  z-index: 99;
  top: calc(50% - 30px) ;
  right: 10%;
  width: 60px;
  height: 60px;
  animation-name: Move2Right;
  animation-duration: 1s;
  animation-iteration-count: infinite;
}

#left-arrow {
  position: absolute;
  opacity: 0.2;
  z-index: 99;
  top: calc(50% - 30px) ;
  left: 10%;
  width: 60px;
  height: 60px;
  animation-name: Move2Left;
  animation-duration: 1s;
  animation-iteration-count: infinite;
}

#like {
  position: absolute;
  opacity: 0.4;
  z-index: 99;
  top: 0;
  width: 60px;

}

#dislike {
  position: absolute;
  opacity: 0.4;
  z-index: 99;
  top: 0;
  width: 60px;
}

.desc-container{
  position: absolute;
  bottom: 20%;
  left: 10%;
  width: 80%;
  height: 20%;
  background-color: rgba(0,0,0,0.3);
  color: white;
}

.blank-container{
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0,0,0,0.7);
  color: white;
  z-index: 999;
}

.blank-container .right, .blank-container .left{
  position: relative;
  display: inline-block;
  height: 100%;
  width: calc(50% - 4px) ;
  top: 25%;
  vertical-align: middle;
}

.blank-container::before{
  position: absolute;
  width: calc(50% - 2px);
  border-right: 2px dashed white;
  content: ' ';
  height: 100%;
}

</style>
