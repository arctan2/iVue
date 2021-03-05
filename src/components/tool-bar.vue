<template>
  <div class="tool-bar" :class='theme + "-tool-bar"'>
    <div class="operations">

      <label class="op add">
        <input accept="image/*" class="tool-bar-image-input" type="file" multiple @input="inputImages">
        <div class="icon">+</div>
        <div class="op-name">add</div>
      </label>

      <div class="op remove" :class="{'disabled-btn': isGridMode}" @click="$emit('removeimage')">
        <div class="icon">-</div>
        <div class="op-name">delete</div>
      </div>

      <div style="display: flex; flex-direction: column; margin-left: 10px; align-items: center;">
        <div class="text" :class='theme + "-text"'>max columns</div>
        <div style="display: flex; flex-direction: row;">
          <input class="c-input" :class='theme + "-c-input"' type="number"
          v-model='maxColumns' min='1'>
          <button class="set" @click="$emit('changemaxcolumns', +maxColumns)">set</button>
        </div>
      </div>


      <div style="margin-left: 10px; display: flex; flex-direction: column;" class="container">
        <button :class="{'disabled-btn': !isGridMode}"
         class="select-mode" @click="$emit('selectmode')">select mode</button>
        <button class="sample-btn" @click="$emit('samplepics')">sample pics</button>
      </div>

      <button style="margin-left: 10px;" 
        :class="{'slide-show-active': isSlideShow, 'disabled-btn': isImagesEmpty}" class="slide-show"
        @click="isSlideShow = !isSlideShow; $emit('slideshow')">
        slide show
      </button>

    </div>
  </div>
</template>

<script>
import gsap from 'gsap';

export default {
  name: 'Topbar',
  props: {
    theme: String,
    isGridMode: Boolean,
    isImagesEmpty: Boolean
  },
  emits: ['imageinput', 'changemaxcolumns', 'removeimage', 'selectmode', 'samplepics', 'slideshow'],
  data(){
    return {
      isIntro: true,
      maxColumns: 6,
      isSlideShow: false
    }
  },
  methods:{
    inputImages(e){
      let images = e.target.files;
      this.$emit('imageinput', images);
      document.querySelector('.tool-bar-image-input').value = '';
    }
  },
  watch: {
    theme(){
      if(this.isIntro)
      gsap.timeline().to('.tool-bar', {width: "50%"}).to('.logo', {pointerEvents: 'all'})
    },
    maxColumns(){
      if(this.maxColumns === '')
        this.maxColumns = '1';
    }
  }
}
</script>

<style scoped>

.tool-bar{
  display: flex;
  flex-direction: row;
  height: 80%;
  width: 0px;
  border-radius: 8px;
  transition: all 0.8s;
  overflow: hidden;
  position: relative;
}

.tool-bar-image-input{
  outline: none;
}

.dark-tool-bar{
  margin-right: 10%;
  background-color: rgb(37, 36, 36);
  box-shadow: 0 0 3px rgb(93, 70, 110);
  border-right: 2px solid rgb(116, 6, 219);
  border-left: 2px solid rgb(116, 6, 219);
}

.light-tool-bar{
  margin-right: 10%;
  background-color: rgb(203, 192, 214);
  box-shadow: 0 0 4px rgb(42, 43, 43);
  border-right: 2px solid rgb(32, 32, 32);
  border-left: 2px solid rgb(32, 32, 32);
}

.operations{
  display: flex;
  flex-direction: row;
  align-items: center;
  padding: 10px;
  overflow-x: auto;
  overflow-y: hidden;
}

.operations::-webkit-scrollbar{
  background-color: transparent;
  height: 8px;
}

.operations::-webkit-scrollbar-thumb{
  background-color: rgb(132, 132, 138);
  border-radius: 50px;
}

.op{
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 50px;
  height: 50px;
  user-select: none;
  border-radius: 6px;
  box-shadow: 0 0 5px rgb(37, 37, 37);
  transition: background-color 0.2s, color 0.2s;
}

.icon{
  font-size: 26px;
}

.op-name{
  position: relative;
  bottom: 8px;
}

.add{
  background: rgb(121, 214, 112);
  color: white;
}

.add:hover{
  background-color: rgb(83, 236, 83);
}

.add:active{
  background-color: rgb(151, 235, 148);
}

.remove{
  background-color: rgb(224, 100, 100);
  color: white;
  margin-left: 10px;
  padding: 5px;
  padding-top: 0px;
}

.select-mode, .sample-btn, .slide-show{
  border-radius: 7px;
  width: 90px;
  height: 25px;
  color: white;
  border: 0;
  outline: none;
  transition: all 0.25s;  
}

.select-mode{
  background-color: rgb(219, 98, 98);
}

.sample-btn{
  margin-top: 4px;
  background-color: rgb(56, 122, 243);
}

.sample-btn:hover{
  background-color: rgb(11, 87, 228);
}

.sample-btn:active{
  background-color: rgb(98, 148, 240);
}

.remove:hover, .select-mode:hover{
  background-color: rgb(233, 43, 43);
}

.remove:active, .select-mode:active{
  background-color: rgb(245, 125, 125);
}

.c-input{
  height: 25px;
  width: 40px;
  outline: none;
  transition: 0.25s;
  background-color: rgb(233, 231, 231);
  border: none;
  padding-left: 3px;
}

.dark-c-input{
  background-color: rgb(78, 76, 76);
  color: aliceblue;
}

.text{
  margin-bottom: 6px;
  white-space: pre;
  transition: all 0.25s;
}

.dark-text{
  color: aliceblue;
}

.light-text{
  color: rgb(58, 57, 57);
}

.set{
  background-color: rgb(91, 92, 182);
  padding-bottom: 2px;
  border-radius: 4px;
  border: none;
  outline: none;
  font-size: 16px;
  color: white;
  width: 40px;
  transition: 0.25s;
  margin-left: 6px;
}

.set:hover{
  background-color: rgb(55, 58, 219);
}

.set:active{
  background-color: rgb(96, 98, 245);  
}

.disabled-btn{
  background-color: rgb(92, 90, 90);
  pointer-events: none;
}

.slide-show{
  background-color: rgb(45, 197, 113);
  color: rgb(255, 255, 255);
  height: 44px;
  width: 70px;
  white-space: pre;
}

.slide-show:hover{
  background-color: rgb(36, 233, 102);
}

.slide-show:active{
  background-color: rgb(13, 197, 83);
}

.slide-show-active, .slide-show-active:hover, .slide-show-active:active{
  background-color: gray;
  color: white;
}

</style>

