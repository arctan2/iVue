<template>
  <div style="position: relative; width: 100%; height: 100%;">
    <div class="images-grid">
      <div v-for="(image, index) of images" :key='image.src' class="img-container"
        :class="`img-idx-${index}`" 
        @click='fullScreenMode'
        @touchstart='longClick' @touchend='release' @mousedown="longClick" @mouseup="release">
        <img :src="image.src" alt="image">
      </div>
    </div>
    <button class="delete-imgs" v-if="isSelectionMode && isGridMode" @click="deleteSelected">
      <span class="x-icon">x</span> delete {{selectedCount}} image(s)
    </button>
    <button class="select-all" 
    v-if="isSelectionMode && (images.length !== selectedCount) && isGridMode" @click="selectAllImages">
      select all
    </button>
  </div>
</template>

<script>

let isSelected;

export default {
  name: 'GridView',
  props: {
    images: Array,
    maxColumns: Number,
    toggleSelectMode: Boolean,
    isGridMode: Boolean
  },
  emits: ['fullscreen', 'multidelete'],
  data(){
    return {
      isSelectionMode: false,
      selectedCount: 0
    }
  },
  methods: {
    changeSelectedCount(){
      let selected = document.querySelectorAll('.selected');
      this.selectedCount = selected.length;
    },
    fullScreenMode(e){
      let target = e.target;
      let imageIndex;
      if(this.isSelectionMode){
        target.classList.toggle('selected');
        if(!document.querySelector('.selected')) this.isSelectionMode = false;
        this.changeSelectedCount();
        return;
      }
      // extracts the index of the selected image
      imageIndex = +target.classList.value.match(/img-idx-\d+/)[0].match(/\d+/)[0];
      this.$emit('fullscreen', imageIndex);
    },
    release(){
      clearTimeout(isSelected);
    },
    longClick(){
      isSelected = setTimeout(() => this.isSelectionMode = true, 500);
    },
    selectAllImages(){
      document.querySelectorAll('.img-container').forEach(ic => ic.classList.add('selected'));
      this.selectedCount = this.images.length;
    },
    deleteSelected(){
      let toDeleteIndexes = [];
      document.querySelectorAll('.selected').forEach(img => {
        toDeleteIndexes.push(img.classList.value.match(/img-idx-\d+/)[0].match(/\d+/)[0]);
      });
      this.$emit('multidelete', toDeleteIndexes);
      this.selectedCount = 0;
      this.isSelectionMode = false;
    }
  },
  watch: {
    toggleSelectMode(){
      this.isSelectionMode = this.toggleSelectMode;
      if(!this.toggleSelectMode){
        this.selectedCount = 0;
        document.querySelectorAll('.selected').forEach(img => img.classList.remove('selected'));
      }
    }
  }
}
</script>

<style scoped>
  .images-grid{
    position: relative;
    display: grid;
    grid-template-columns: repeat(6, 120px);
    grid-column-gap: 2px;
    padding-top: 103px;
    padding-left: 3px;
    width: 100%;
    height: 100%;
    transform-style: preserve-3d;
    opacity: 0;
    overflow: auto;
    row-gap: 0px;
  }

  .images-grid::-webkit-scrollbar{
    width: 10px;
    height: 10px;
    background-color: rgba(63, 62, 62, 0.356);
  }

  .images-grid::-webkit-scrollbar-thumb{
    background-color: rgba(102, 101, 101, 0.938);
    border-radius: 50px;
  }

  .img-container{
    width: 120px;
    height: 120px;
    background-color: rgba(97, 99, 99, 0.664);
    transition: transform 0.15s;
    user-select: none;
  }

  .img-container:hover{
    transform: scale(0.9, 0.9);
  }

  .img-container:active{
    transform: scale(0.8, 0.8);
  }

  .img-container img{
    height: 100%;
    width: 100%;
    object-fit: cover;
    -webkit-user-drag: none;
    user-select: none;
    pointer-events: none;
  }

  .selected{
    transform: scale(0.8, 0.8);
    opacity: 0.7;
    animation: selected-animation 2s infinite;
    transition: all 0.25s;
  }

  .delete-imgs, .select-all{
    font-weight: bold;
    left: 50%;
    border-radius: 20px;
    transform: translate(-50%, -50%) scale(0, 0);
    outline: none;
    animation: pop 1s forwards;
    position: fixed;
    color: rgb(236, 215, 215);
    backdrop-filter: blur(20px);
    height: 40px;
    transition: 0.2s;
  }

  .delete-imgs{
    border: 1px solid rgb(95, 91, 91);
    width: 180px;
    background-color: rgba(255, 0, 0, 0.486);
    padding-left: 7px;
    bottom: 20%;
  }

  .x-icon{
    display: flex;
    position: absolute;
    left: 8px;
    font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
    justify-content: center;
    align-items: center;
    transform: translate(-15%, -20%);
    padding-bottom: 3px;
    width: 30px;
    height: 30px;
    font-size: 26px;
    border: 1px solid rgb(241, 16, 16);
    color: rgb(221, 208, 208);
    border-radius: 50px;
  }

  .select-all{
    bottom: 10%;
    width: 100px;
    font-size: 16px;
    border: 1px solid rgb(0, 255, 0);
    background-color: rgba(0, 255, 0, 0.52);
  }

  .delete-imgs:hover, .select-all:hover{
    box-shadow: 0 0 5px gray;
  }

  @keyframes pop {
    100%{
      transform: translate(-50%, -50%) scale(1, 1);
    }
  }

  @keyframes selected-animation{
    0%{
      transform: scale(0.8, 0.8);
    }
    50%{
      transform: scale(0.9, 0.9);
    }
    100%{
      transform: scale(0.8, 0.8);
    }
  }

</style>