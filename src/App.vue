<template>
  <div id="main">
    <Background @begin="beginAnimation" :bTheme='theme' />

    <Topbar :theme="theme" :isOpen="isOpen" :isGridMode='isGridMode' :isHelpOpen='isHelpOpen'
      :isImagesEmpty='isImagesEmpty'
      @themechange='changeTheme'
      @imageinput='imageInput'
      @changemaxcolumns='changeMaxColumns'
      @removeimage='removeImage'
      @selectmode='selectMode'
      @samplepics='samplePics'
      @help='help'
      @slideshow='slideShow'
    />

    <WorkSpace
      :isGridMode='isGridMode'
      :isImagesEmpty='isImagesEmpty'
      :images='images'
      :theme='theme'
      :maxColumns='maxColumns'
      :remove='remove'
      :toggleSelectMode='toggleSelectMode'
      :isNotIntro='isNotIntro'
      :isSlideShow='isSlideShow'

      @imageinput='imageInput'
      @deleteimage='deleteImage'
      @multidelete='multiDelete'
    />

    <Help :theme='theme' :isHelpOpen='isHelpOpen' @help='isHelpOpen = !isHelpOpen'/>

    <div>
      <div class="top-bar-open" ref="topbar"
      :class="{
          'open': isOpen,
          'dark-drop-down-icon': isDarkTheme,
          'light-drop-down-icon': isDarkTheme === false,
          'disable-open-arrow': isHelpOpen || isSlideShow
        }"
      @click='toggleOpen'></div>
      <div class="toggle-grid-icon" ref="gridIcon" @click="changeViewMode"
        v-if="(images.length !== 0) && !isSlideShow"
      >
        <div v-for='i in 4' :key=i class="grid-cell"></div>
      </div>
    </div>

  </div>
</template>

<script>
import Background from './components/mains/background';
import Topbar from './components/mains/top-bar';
import WorkSpace from './components/mains/work-space';
import gsap from 'gsap';
import Help from './components/help';

function random(){
  return Math.floor(Math.random() * (999 - 500) + 500);
}

export default {
  name: 'App',
  components: {
    Background,
    Topbar,
    WorkSpace,
    Help
  },
  data(){
    return{
      theme: null,
      images: [],
      isImagesEmpty: false,
      isOpen: true,
      isHelpOpen: false,
      isDarkTheme: false,
      maxColumns: 6,
      remove: 0,
      isGridMode: false,
      toggleSelectMode: false,
      isNotIntro: false,
      isSlideShow: false
    }
  },
  methods: {
    beginAnimation(theme){
      this.theme = theme;
      this.isOpen = true;
      // this is because the add-img icon pops up too early
      this.isImagesEmpty = true;
      if(theme === 'dark') this.isDarkTheme = true;
      else this.isDarkTheme = false;
      gsap.to('.top-bar-open', {opacity: 1, delay: 9, pointerEvents: 'all'})
      setTimeout(() => this.isNotIntro = true, 9500)
      setTimeout(() => this.$refs.topbar.click(), 2000)
    },
    help(){
      this.isHelpOpen = !this.isHelpOpen;
    },
    toggleOpen(){
      this.isOpen = !this.isOpen;
    },
    slideShow(){
      this.toggleOpen();
      this.isSlideShow = !this.isSlideShow;
    },
    changeTheme(){
      this.isDarkTheme = !this.isDarkTheme;
      switch(this.theme){
        case 'dark': 
          this.theme = 'light';
          break;
        case 'light': 
          this.theme = 'dark';
          break;
      }
    },
    changeMaxColumns(newMaxColumns){
      this.maxColumns = newMaxColumns;
    },
    imagesSize(){
      this.images.length === 0 ? this.isImagesEmpty = true : this.isImagesEmpty = false;
    },
    imageInput(images){
      let filteredImgs = [];
      images.forEach(img => {
        for(let image of this.images) if(image.name === img.name) return;
        // images' image-object-structure
        filteredImgs.push({src: URL.createObjectURL(img), name: img.name});
      })
      this.images = this.images.concat(filteredImgs);
    },
    // removeImages is to get the index from the workspace component which then runs deleteImages 
    removeImage(){
      this.remove = this.remove + 1;
    },
    deleteImage(index){
      this.images.splice(index, 1);
      this.imagesSize();
    },
    multiDelete(toDeleteIndexes){
      if(toDeleteIndexes.length === this.images.length) this.images = [];
      else{
        toDeleteIndexes = toDeleteIndexes.sort().reverse();
        for(let index of toDeleteIndexes)
          this.images.splice(index, 1);
      }
        
      this.$refs.gridIcon.classList.add('disabled-grid-icon');
      this.imagesSize();
    },
    changeViewMode(){
      let tl = gsap.timeline({defaults: {duration: 0.5, ease: 'power1.in'}});
      if(!this.isGridMode)
        tl
          .to('.grid-cell:nth-child(1)', 
          {borderBottom: 0, borderRight: 0, x: 2, y: 2, borderRadius: 0})
          .to('.grid-cell:nth-child(2)', 
          {borderBottom: 0, borderLeft: 0, x: -2, y: 2, borderRadius: 0}, '-=0.4')
          .to('.grid-cell:nth-child(4)', 
          {borderTop: 0, borderLeft: 0, x: -2, y: -2, borderRadius: 0}, '-=0.6')
          .to('.grid-cell:nth-child(3)', 
          {borderTop: 0, borderRight: 0, x: 2, y: -2, borderRadius: 0}, '-=0.8')
      else
        tl
          .to('.grid-cell', {border: '3px solid black', x: 0, y: 0, borderRadius: 5 })
      this.isGridMode = !this.isGridMode;
    },
    selectMode(){
      this.toggleSelectMode = !this.toggleSelectMode;
    },
    samplePics(){
      let samples = [];
      let l = this.images.length;
      for(let i = 0; i < 5; i++){
        samples.push({src: `https://picsum.photos/${random()}/${random()}`, name: `image-${l}`});
        l++;
      }
      this.images = [...this.images, ...samples];
    }
  },
  watch: {
    images(){
      this.imagesSize();
    },
    maxColumns(){
      gsap.to('.images-grid', {gridTemplateColumns: `repeat(${this.maxColumns}, 120px)`, duration: 0});
    }
  }
}

</script>

<style>
*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

#main{
  width: 100%;
  height: 100vh;
  overflow: hidden;
}

body{
  overflow: hidden;
}

.dont-show{
  opacity: 0;
  pointer-events: none;
}

.top-bar-open{
  position: fixed;
  user-select: none;
  right: 5%;
  top: 25px;
  height: 0px;
  width: 0px;
  transform: rotate(180deg) translate(0%, 30%);
  opacity: 0;
  transition: all 1s;
  z-index: 6;
  border: solid transparent 25px;
}

.dark-drop-down-icon{
  border-top: solid rgb(241, 235, 235) 25px;
}

.light-drop-down-icon{
  border-top: solid black 25px;
}

.open{
  transform: rotate(0deg) translate(0%, 30%);
}

.dev-btn{
  position: fixed;
  top: 20px;
  left: 3px;
  z-index: 5;
}

.toggle-grid-icon{
  display: grid;
  grid-template-columns: auto auto;
  grid-template-rows: auto auto;
  align-items: center;
  justify-items: center;
  position: fixed;
  z-index: 3;
  width: 60px;
  height: 60px;
  bottom: 10%;
  left: 5%;
  transform: scale(0, 0);
  animation: pop 0.8s forwards;
}

.grid-cell{
  width: 90%;
  height: 90%;
  border-radius: 5px;
  border: 3px solid black;
  transition: filter 0.25s;
  filter: drop-shadow(0 0 5px rgb(136, 135, 135));
}

.disabled-grid-icon{
  pointer-events: none;
  filter: drop-shadow(0 0 5px rgb(243, 12, 12));
}

.disable-open-arrow{
  border: 0;
  transform: scaleX(0);
  pointer-events: none;
}

@keyframes pop {
  100%{
    transform: scale(1, 1);
  }
}

@media (max-width: 400px){
  .top-bar-open{
    right: 5%;
    top: 100px;
  }
}

</style>
