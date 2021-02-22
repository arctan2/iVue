<template>
  <div class="top-bar" :class="theme">
    <LogoIntro
     :theme='logoTheme' 
     @introend='toolBar' 
     @click="$emit('themechange')"
     />
    <ToolBar :theme='toolBarTheme' :isGridMode='isGridMode'
     @imageinput='imageInput'
     @changemaxcolumns='changeMaxColumns'
     @removeimage='$emit("removeimage")'
     @selectmode='$emit("selectmode")'
     @samplepics='$emit("samplepics")'
     />

    <button v-if="theme" class="help-icon"
      @click="$emit('help')"
      :class="{'help-icon-open': isHelpOpen}"
    >?</button>
  </div>
</template>

<script>
import LogoIntro from '../logo-intro'
import ToolBar from '../tool-bar'
import gsap from 'gsap'

export default {
  name: 'Topbar',
  components: {
    LogoIntro,
    ToolBar
  },
  emits: ['themechange', 'imageinput', 
    'removeimage', 'changemaxcolumns', 'help', 'selectmode', 'samplepics'],
  props: {
    theme: String,
    isOpen: Boolean,
    isGridMode: Boolean,
    isHelpOpen: Boolean
  },
  data(){
    return {
      logoTheme: null,
      toolBarTheme: null,
      isIntro: true
    }
  },
  methods: {
    toolBar(){
      this.toolBarTheme = this.theme;
    },
    imageInput(images){
      this.$emit('imageinput', images);
    },
    changeMaxColumns(newMaxColumn){
      this.$emit('changemaxcolumns', newMaxColumn);
    }
  },
  watch: {
    theme(){
      if(this.isIntro)
        gsap.timeline()
          .to('.top-bar', {y: '100%', display: 'flex', opacity: 1, duration: 0.8})
          .call(() => {
            this.logoTheme = this.theme;
            this.isIntro = false;
          })
      else {
        this.logoTheme = this.theme;
        this.toolBarTheme = this.theme;
      }
    },
    isOpen(){
      if(this.isOpen){
        gsap.timeline()
          .to('.top-bar-open', {pointerEvents: 'none', duration: 0})
          .to('.top-bar', {rotate: -5, y: '30%', ease: 'power2.in'})
          .to('.top-bar', {rotate: 0, y: "0%", ease: 'power2.out'}, '-=0.2')
          .to('.top-bar-open', {pointerEvents: 'all', duration: 0})
      }
      else {
        gsap.timeline()
          .to('.top-bar-open', {pointerEvents: 'none', duration: 0})
          .to('.top-bar', {y: '100%'})
          .to('.top-bar-open', {pointerEvents: 'all', duration: 0})
      }
    }
  }
}
</script>

<style scoped>

.top-bar{
  position: absolute;
  display: none;
  opacity: 0;
  z-index: 4;
  height: 100px;
  width: 100%;
  flex-direction: row;
  justify-content: space-around;
  align-items: center;
  top: -100px;
  transition: background-color 0.8s;
  user-select: none;
  overflow: hidden;
}

.help-icon{
  position: fixed;
  outline: none;
  border: 0;
  width: 50px;
  height: 50px;
  font-size: 1.2rem;
  box-shadow: 0 0 5px grey;
  font-weight: bold;
  background: linear-gradient(-20deg, rgb(111, 97, 194), rgb(197, 117, 228));
  color: rgb(255, 255, 255);
  top: -50px;
  right: -50px;
  z-index: 1;
  border-bottom-left-radius: 50px;
  transition: 0.25s;
  animation-name: pop;
  animation-fill-mode: forwards;
  animation-delay: 10s;
  animation-duration: 1s;
}

.help-icon-open{
  transform: scale(2, 2) translate(-5px, 5px);
}

@keyframes pop {
  100%{
    top: -10px;
    right: -10px;
  }
}

.dark{
  background: linear-gradient(150deg, rgb(37, 38, 43), rgb(38, 43, 37));
  border-bottom: 1px solid rgb(174, 50, 231);
  box-shadow: 0 0 6px rgb(68, 61, 68);
}

.light{
  background: linear-gradient(150deg, rgb(191, 158, 235), rgb(189, 228, 202));
  box-shadow: 0 0 4px rgb(99, 100, 99);
}

</style>

