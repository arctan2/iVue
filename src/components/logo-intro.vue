<template>
  <div class="logo">
    <div class="text" :class="theme + '-text-m'">i</div>
    <span class="fade-text" :class="theme + '-text-sub'">MAGE </span>
    <span class="text vue" :class="theme + '-vue'">Vue</span>
  </div>
</template>

<script>

import gsap from 'gsap'

export default {
  name: 'Topbar',
  props: {
    theme: String
  },
  emits: ['introend'],
  data(){
    return {
      isIntro: true
    }
  },
  watch: {
    theme(){
      // Idk why but it shows .<theme>-text-m target not found, but this below thing fix it
      // I think before the theme is loaded the gsap runs and hence it could'nt find it
      if(this.isIntro)
      setTimeout(() => {
        gsap.timeline()
          .to('.logo', {pointerEvents: 'none'})
          .to(`.${this.theme}-text-m`, {opacity: 1, duration: 1, delay: 0.1, ease: 'power3.in'})
          .to('.vue', {width: "100%"})
          .to('.vue', {opacity: 1, ease: 'power2.out'}, '-=0.2')
          .to('.vue', {overflowX: 'visible'})
          .to('.fade-text', {width: '100%', duration: 1, ease: 'power3.in'})
          .to('.fade-text', {opacity: 1, ease: 'power3.out', duration: 2})
          .to('.fade-text', {width: 0, scaleY: 0.8, scaleX: 0.8, delay: 0.2, duration: 0.8})
          .call(() => {
            this.$emit('introend');
            this.isIntro = false;
          })
      }, 1)
    }
  }
}
</script>

<style scoped>
.logo{
  display: flex;
  flex-direction: row;
  justify-content: center;
  width: max-content;
  margin-left: 20%;
  user-select: none;
}

.text{
  font-size: 40px;
  display: inline-block;
  opacity: 0;
  font-family: 'Lucida Sans', 'Lucida Sans Regular', 'Lucida Grande', 'Lucida Sans Unicode', Geneva, Verdana, sans-serif;
}

.dark-text-m{
  color: rgb(196, 238, 238);
}

.dark-text-sub{
  color: rgb(156, 149, 149);
}

.light-text-m{
  color: rgb(78, 76, 76);
}

.light-text-sub{
  color: rgb(124, 102, 102);
}

.fade-text{
  display: flex;
  align-items: center;
  font-size: 26px;
  width: 0%;
  overflow: hidden;
  opacity: 0;
}

.vue{
  width: 0%;
  overflow-x: hidden;
}

.dark-vue{
  color: rgb(26, 218, 26);
}

.light-vue{
  color: rgb(243, 243, 243);
}

@media (max-width: 500px){
  .logo{
    margin-left: 10%;
  }
}

</style>

