<template>
  <div class="text-container" v-if="showOptions">
    Choose your theme
  </div>
  <div class="final-background" :class="color ? color : ''">
    <div v-if="showOptions" ref="options" class="options" @click='pickTheme'></div>
  </div>
</template>

<script>

import gsap from 'gsap';

export default {
  name: 'Background',
  data(){
    return{
      v1: [],
      x: null,
      y: null,
      color: null,
      showOptions: true,
      rotateDeg: 0,
      refresh: null,
      theme: null
    }
  },emits: ['begin'],
  props: {
    bTheme: String
  },
  methods: {
    pickTheme(e){
      let xA = e.clientX;
      let yA = e.clientY;
      let v2 = [-xA, this.y - yA];
      let xp = this.v1[0]*v2[1] - this.v1[1]*v2[0];
      if (xp > 0){
        this.changeTheme('light');
        this.theme = 'light';
      }else if (xp < 0){
        this.changeTheme('dark');
        this.theme = 'dark';
      }
      this.$refs.options.style.pointerEvents = 'none';
      window.removeEventListener('resize', this.changeEvent);
    },
    changeTheme(color){
      this.color = color;
      let x = '100%';
      if(color === 'light') x = '-100%'
      gsap.timeline()
        .to('.options', {rotate: -this.rotateDeg, duration: 1.4, ease: "power2.in"})
        .to('.options', {x, duration: 1.5, ease: "power1.in"})
        .to('.text-container', {opacity: 0}, "-=0.8")
        .to('#work-space', {zIndex: 3})
        .call(() => {
          this.showOptions = false;
          this.$emit('begin', this.theme);
        });
    },
    changeEvent(){
      clearInterval(this.refresh);
      this.refresh = setTimeout(() => {
        let options = this.$refs.options;

        if(this.y !== options.clientHeight)
          this.y = options.clientHeight
        if(this.x !== options.clientWidth)
          this.x = options.clientWidth

        this.refresh = null;
      }, 500)
    }
  },
  watch: {
    x(){
      this.v1[0] = -this.x;
      this.rotateDeg = Math.atan(this.x/this.y) * (180 / Math.PI)
    },
    y(){
      this.v1[1] = this.y;
      this.rotateDeg = Math.atan(this.x/this.y) * (180 / Math.PI)
    },
    bTheme(){
      this.color = this.bTheme;
    }
  },
  mounted(){
    let options = this.$refs.options;
    this.x = options.clientWidth;
    this.y = options.clientHeight;
    this.v1 = [-this.x, this.y];
    this.rotateDeg = Math.atan(this.x/this.y) * (180 / Math.PI)
    window.addEventListener('resize', this.changeEvent)
  }
}

</script>

<style scoped>

.final-background{
  position: absolute;
  height: 100vh;
  width: 100vw;
  overflow: hidden;
  transition: background-color 0.8s;
  z-index: 2;
}

.options{
  width: 100%;
  height: 100%;
  background: linear-gradient(to top left, rgb(211, 203, 203) 49.9%, rgb(20, 20, 20) 50%) left 100%;
  transform: scale(5, 5);
}

.dark{
  background-color: rgb(20, 20, 20);
}

.light{
  background-color: rgb(211, 203, 203);
}

.text-container{
  z-index: 3;
  font-size: 32px;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  white-space: pre;
  color: rgb(255, 255, 255);
  mix-blend-mode: difference;
  user-select: none;
  pointer-events: none;
}

</style>
