<template>
  <div id="nav-layout"
    @click='navTo'
    @touchstart='beginPoint'
    @touchmove='dragStart'
    @touchend='endPoint'
  ></div>
</template>

<script>

let startPos;
let endPos;
let elapsedTime;

export default {
  name: 'NavLayout',
  data(){
    return {
      h: 0,
      w: 0,
      nl: 0,
      refresh: null,
      hD3: 0,
      wD3: 0
    }
  },
  props: {
    preventNav: Boolean
  },
  emits: ['nav'],
  methods: {
    computeDirections(x, y, wf, hf){
      for(let i = 0; i < 3; i++){
        if(x < wf){
          wf = i;
          break;
        }
        wf += wf;
      }
      for(let i = 0; i < 3; i++){
        if(y < hf){
          hf = i;
          break;
        }
        hf += hf;
      }
      return (3 * hf + wf);
    },
    navTo(e){
      if(this.preventNav) return;
      let tb = document.querySelector('.top-bar-open');
      if(!tb.classList.contains('open')) tb.click();
      let goTo = this.computeDirections(e.clientX, e.clientY, this.wD3, this.hD3);
      this.$emit('nav', goTo);
    },
    changes(){
      clearInterval(this.refresh);
      setTimeout(() => {
        if(this.nl.clientHeight !== this.h) this.h = this.nl.clientHeight;
        if(this.nl.clientWidth !== this.w) this.w = this.nl.clientWidth;
      }, 500)
    },
    swipeNav(){
      if(this.preventNav) return;
      elapsedTime = endPos.time - startPos.time;
      let xDist = endPos.x - startPos.x;
      let yDist = endPos.y - startPos.y;
      let dir;
      if(elapsedTime < 500){
        if(Math.abs(xDist) >= 20 && Math.abs(yDist) <= 150){
          dir = xDist < 0 ? 5 : 3;
        }else if(Math.abs(yDist) >= 20 && Math.abs(xDist) <= 150){
          dir = yDist < 0 ? 7 : 1;
        }
      }
      if(dir){
        let tb = document.querySelector('.top-bar-open');
        if(!tb.classList.contains('open')) tb.click();
        this.$emit('nav', dir);
      }
        
    },
    beginPoint(e){
      let touch = e.touches[0];
      let x = touch.clientX;
      let y = touch.clientY;
      let time = new Date().getTime();
      startPos = { x, y, time }
    },
    endPoint(e){
      let touch = e.changedTouches[0];
      let x = touch.clientX;
      let y = touch.clientY;
      let time = new Date().getTime();
      endPos = { x, y, time }
      this.swipeNav();
    }
  },
  watch: {
    h(){
      this.hD3 = this.h/3;
    },
    w(){
      this.wD3 = this.w/3;
    }
  },
  mounted(){
    this.nl = document.getElementById('nav-layout');
    this.h = this.nl.clientHeight;
    this.w = this.nl.clientWidth;
    this.hD3 = this.h/3;
    this.wD3 = this.w/3;
    window.addEventListener('resize', this.changes);
  }
}
</script>

<style scoped>

#nav-layout{
  position: fixed;
  z-index: 2;
  width: 100vw;
  height: 100vh;
}

</style>