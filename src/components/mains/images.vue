<template>
  <div class="images">
    <div class="current-img">
      <img v-if="currentImg" :src='currentImg.src' alt="loading">
    </div>
    <div class="next-img">
      <img v-if="nextImg" :src='nextImg.src' alt="loading">
    </div>
  </div>
</template>

<script>
import gsap from 'gsap';

export default {
  name: 'Images',
  props: {
    images: Array,
    next: Object,
    currentIndex: Number,
    del: Number,
    reRenderImages: Boolean
  },
  data(){
    return {
      currentImg: null,
      nextImg: null
    }
  },
  watch: {
    reRenderImages(){
      this.currentImg = this.images[this.currentIndex];
      this.nextImg = this.currentImg;
    },
    images(){
      this.currentImg = this.images[0];
    },
    next(){
      let c = this.next.currentObj;
      let n = this.next.nextObj;
      this.nextImg = this.images[this.currentIndex];
      let tl = gsap.timeline();
      tl
        .to('#nav-layout', {pointerEvents: 'none', duration: 0})

      if(this.next.prevent)
        tl
          .to('.next-img', {x: '100%', y: '100%', duration: 0})
          .to('.current-img', {
          x: `${c.x/5}%`, y: `${c.y/5}`, duration: 0.3, rotateX: c.rx/2, rotateY: c.ry/2})
          .to('.current-img', {x: 0, y: 0, rotateX: 0, rotateY: 0, ease: 'bounce.out'})
          .to('.next-img', {x: 0, y: 0, duration: 0})
      else
        tl
          .to('.current-img', {
            x: `${c.x}%`, y: `${c.y}%`, duration: 1, rotateX: c.rx, rotateY: c.ry,
            onComplete: () => this.currentImg = this.nextImg})
          .from('.next-img', {x: `${n.x}%`, y: `${n.y}%`, rotateX: n.rx, rotateY: n.ry}, '-=0.8')
          .to('.current-img', {
            x: '0%', y: '0%', duration: 0, rotateX: '0deg', rotateY: '0deg'})
    
      tl.to('#nav-layout', {pointerEvents: 'all', duration: 0})
    },
    del(){
      let d = '-100%';
      this.nextImg = this.images[this.currentIndex];
      if(this.currentIndex !== this.images.length - 1) d = '100%';
      gsap.timeline()
        .to('.next-img', {x: d, duration: 0})
        .to('.current-img', {scaleX: 0, scaleY: 0, opacity: 0.2})
        .to('.next-img', {x: '0%'}, '-=0.5')
        .call(() => this.currentImg = this.nextImg)
        .to('.current-img', {scaleX: 1, scaleY: 1, duration: 0, opacity: 1})
    }
  }
}

</script>

<style scoped>

.images{
  position: relative;
  perspective: 500px;
}

.images div{
  width: 100vw;
  height: 100vh;
  transform-style: preserve-3d;
  position: absolute;
}

.images div img{
  width: 100%;
  height: 100%;
  object-fit: contain;
}

</style>

