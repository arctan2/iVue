<template>
  <div id="work-space">
    <NavLayout @nav='navTo' :preventNav='preventNav' v-if="!isGridMode"/>
    <div style='position: relative; width: 100vw; height: 100vh;'>
      <Images
      :images='images'
      :currentIndex='currentIndex'
      :next='next' 
      :del='del'
      :reRenderImages='reRenderImages'
      />

      <GridView
       :images='images' 
       :maxColumns='maxColumns'
       :toggleSelectMode='toggleSelectMode'
       :isGridMode='isGridMode'
       @fullscreen='selectAndExitGridMode'
       @multidelete='multiDelete'
      />

    </div>
    
    <label class='add-new-images' :class="{'show': isImagesEmpty && isNotIntro}">
      <input accept="image/*" class="image-input" type="file" multiple @input="inputImages">
      <span class="icon">
        +
      </span>
    </label>
  </div>
</template>

<script>
import Images from './images';
import NavLayout from '../nav-layout';
import GridView from '../grid-view';
import gsap from 'gsap';

export default {
  name: 'WorkSpace',
  components: {
    Images,
    NavLayout,
    GridView
  },
  data(){
    return {
      next: {},
      currentIndex: 0,
      rowLimits: {
        upper: 9,
        lower: 0
      },
      del: 0,
      preventNav: true,
      reRenderImages: false
    }
  },
  props: {
    isImagesEmpty: Boolean,
    theme: String,
    images: Array,
    maxColumns: Number,
    remove: Number,
    isGridMode: Boolean,
    toggleSelectMode: Boolean,
    isNotIntro: Boolean
  },
  emits: ['imageinput', 'deleteimage', 'multidelete'],
  methods: {
    inputImages(e){
      let images = e.target.files;
      this.$emit('imageinput', images);
      document.querySelector('.image-input').value = '';
    },
    changeRowLimits(){
      this.rowLimits.lower = parseInt(this.currentIndex / this.maxColumns) * this.maxColumns;
      this.rowLimits.upper = this.rowLimits.lower + this.maxColumns - 1;
    },
    getAnimationObject(x, y, rx, ry){
      return {
        x, y, rx, ry
      }
    },
    navTo(dir){
      let currentObj;
      let nextObj;
      let prevent = false;
      let nextImageNotFound;

      switch(dir){
        case 1:
          nextImageNotFound = this.currentIndex - this.maxColumns < 0;
          currentObj = this.getAnimationObject(0, 150, -30, 0);
          nextObj = this.getAnimationObject(0, -100, -30, 0);
          if(nextImageNotFound){
            prevent = true;
            break;
          }
          this.currentIndex -= this.maxColumns;
          this.changeRowLimits();
          break;

        case 7:
          nextImageNotFound = this.currentIndex + this.maxColumns > this.images.length - 1;
          currentObj = this.getAnimationObject(0, -150, 30, 0);
          nextObj = this.getAnimationObject(0, 120, 30, 0);
          if(nextImageNotFound){
            prevent = true;
            break;
          }
          this.currentIndex += this.maxColumns;
          this.changeRowLimits();
          break;

        case 3:
          nextImageNotFound = this.currentIndex - 1 < this.rowLimits.lower;
          currentObj = this.getAnimationObject(150, 0, 0, 30);
          nextObj = this.getAnimationObject(-120, 0, 0, 30);
          if(nextImageNotFound){
            prevent = true;
            break;
          }
          this.currentIndex -= 1;
          break;

        case 5:
          nextImageNotFound = 
            (this.currentIndex + 1 > this.rowLimits.upper) 
              ||
            (this.currentIndex + 1 > this.images.length - 1);
          currentObj = this.getAnimationObject(-150, 0, 0, -30);
          nextObj = this.getAnimationObject(120, 0, 0, -30);
          if(nextImageNotFound){
            prevent = true;
            break;
          }
          if(this.currentIndex + 1 > this.images.length - 1) break;
          this.currentIndex += 1;
          break;

        case 0:
          nextImageNotFound = 
            (this.currentIndex - this.maxColumns - 1) < this.rowLimits.lower - this.maxColumns
             ||
            (this.currentIndex - this.maxColumns < 0);
          currentObj = this.getAnimationObject(150, 150, -30, 30)
          nextObj = this.getAnimationObject(-120, -100, -30, 30)
          if(nextImageNotFound){
            prevent = true;
            break;
          }
          this.currentIndex -= this.maxColumns + 1;
          this.changeRowLimits();
          break;

        case 2:
          nextImageNotFound = 
            ((this.currentIndex - this.maxColumns) + 1) > this.rowLimits.upper - this.maxColumns
              ||
            (this.currentIndex - this.maxColumns < 0);
          currentObj = this.getAnimationObject(-150, 150, -30, 30)
          nextObj = this.getAnimationObject(120, -100, -30, -30)
          if(nextImageNotFound){
            prevent = true;
            break;
          }
          this.currentIndex -= this.maxColumns;
          this.currentIndex++;
          this.changeRowLimits();
          break;

        case 6:
          nextImageNotFound = 
            (this.currentIndex + this.maxColumns - 1) < this.rowLimits.lower + this.maxColumns
              ||
            (this.currentIndex + this.maxColumns - 1) > this.images.length - 1;
          currentObj = this.getAnimationObject(150, -150, -30, 30);
          nextObj = this.getAnimationObject(-120, 120, -30, 30);
          if(nextImageNotFound){
            prevent = true;
            break;
          }
          this.currentIndex += this.maxColumns - 1;
          this.changeRowLimits();
          break;

        case 8:
          nextImageNotFound = 
            (this.currentIndex + this.maxColumns + 1) > this.rowLimits.upper + this.maxColumns
             ||
            (this.currentIndex + this.maxColumns + 1) > this.images.length - 1;
          currentObj = this.getAnimationObject(-150, -150, -30, -30)
          nextObj = this.getAnimationObject(120, 120, -30, -30)
          if(nextImageNotFound){
            prevent = true;
            break;
          }
          this.currentIndex += this.maxColumns + 1;
          this.changeRowLimits();
          break;
        default: return;
      }
      this.next = { currentObj, nextObj , prevent }
    },
    selectAndExitGridMode(selectedImageIndex){
      this.currentIndex = selectedImageIndex;
      this.reRenderImages = !this.reRenderImages;
      let gridIcon = document.querySelector('.toggle-grid-icon');
      gridIcon.click();
      gridIcon.classList.remove('disabled-grid-icon');
      this.changeRowLimits();
    },
    multiDelete(toDeleteIndexes){
      this.$emit('multidelete', toDeleteIndexes)
    }
  },
  watch: {
    maxColumns(){
      this.changeRowLimits();
    },
    remove(){
      this.$emit('deleteimage', this.currentIndex);
      if((this.currentIndex === this.images.length) && (this.images.length !== 0)) this.currentIndex--;
      this.del++;
    },
    isImagesEmpty(){
      this.images.length === 0 ? this.preventNav = true : this.preventNav = false;
    },
    isGridMode(){
      let tl = gsap.timeline();
      if(this.isGridMode)
        tl
          .to('.images', {scaleX: 1.5, duration: 1, rotateX: 60, scaleY: 1.5, opacity: 0})
          .to('.images', {display: 'none', duration: 0})
          .to('.images-grid', {display: 'block', duration: 0}, '-=1')
          .fromTo('.images-grid',
            {opacity: 0, rotateX: 70, scaleX: 0.6, scaleY: 0.6, duration: 0},
            {opacity: 1, rotateX: 0, scaleX: 1, scaleY: 1, display: 'grid'},
            '-=0.5'
            )
      else
        tl
          .to('.images-grid', {opacity: 0, scaleX: 0.6, rotateX: -70, scaleY: 0.6})
          .to('.images-grid', {display: 'none', duration: 0})
          .to('.images', {display: 'block', duration: 0}, '-=1')
          .fromTo('.images', 
            {scaleX: 1.5, scaleY: 1.5, opacity: 0, rotateX: -60}, 
            {scaleX: 1, scaleY: 1, opacity: 1, rotateX: 0}, 
            '-=0.5'
            )
    }
  }
}
</script>

<style scoped>

#work-space{
  position: relative;
  z-index: 1;
  width: 100vw;
  height: 100vh;
  user-select: none;
  perspective: 1000px;
}

.add-new-images{
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%) scale(0, 0);
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 80px;
  height: 80px;
  user-select: none;
  border-radius: 6px;
  box-shadow: 0 0 5px rgb(58, 56, 56);
  transition: all 0.3s;
  background-color: rgb(53, 54, 52);
  color: rgb(204, 195, 195);
  z-index: 3;
}

.add-new-images:hover{
  background-color: rgb(79, 77, 87);
  color: rgb(160, 138, 238);
  box-shadow: 0 0 5px rgb(21, 255, 0);
}

.add-new-images:active{
  background-color: rgb(77, 77, 77);
  color: rgb(191, 184, 214);
  box-shadow: 0 0 10px rgb(21, 255, 0);  
}

.show{
  animation-name: pop;
  animation-duration: 1s;
  animation-fill-mode: forwards;
}

@keyframes pop {
  100%{
    transform: translate(-50%, -50%) scale(1, 1);
  }
}

.icon{
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  font-size: 70px;
  font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
}

.image-input{
  display: none;
}

</style>