<template>
  <div class="help" :class="{'help-active': isHelpOpen}" @click="$emit('help')">
    <transition enter-active-class='help-open' leave-active-class="help-close">

      <section v-if="isHelpOpen" class="help-dialogue-box">

        <div style='overflow: auto; height: 100%'>
            <h1 style="text-align: center; font-size: 40px;">
              Welcome to i<span style="color: rgb(75, 72, 72);">Vue</span>
            </h1>
          <br>
          <section>
              <h4 style="text-align: center; color: grey;">just add images and "Vue" them!</h4>
              <br>
              <h1>What makes iVue different?</h1>
              <h4 style="margin-left: 5px; color: rgb(58, 98, 98)"> - 8 direction navigation</h4>
              <br>
              <h1 style="font-size: 24px;">How to use:</h1>
              <br>
              <section>
                <h3>Add the images</h3>
                <br>
                <p>
                  You can add your images by browsing through your storage using the add button.
                    <br>
                  Or if you are someone who is testing this you can click on the 'sample pics' 
                  button in the tool-bar to get some random photos.
                  
                </p>
              </section>
              <br>
              <section>
                <h3>Navigating Images</h3>
                <br>
                <p>
                  Navigation is very simple, just click on the screen to navigate to next photo.
                  <br>
                  The nav-layout is as follows. 
                </p>
                <br>
                <div class="help-nav-grid">
                    <div v-for="(nav, index) of navs" class="help-grid-cell" :key='index'>{{nav}}</div>
                </div>
                <br>
                <div style="font-size: 14px; text-align: center;">
                  you can swipe to navigate on touch devices.
                </div>
              </section>
          </section>

        </div>

      </section>

    </transition>
  </div>
</template>

<script>
export default {
  name: 'Help',
  emits: ['help'],
  props: {
    theme: String,
    isHelpOpen: Boolean
  },
  data(){
    return{
      navs: [
        'dia', 'up', 'dia', 'left', '', 'right', 'dia', 'down', 'dia'
      ]
    }
  }
}
</script>

<style scoped>

.help{
  background-color: rgba(0, 0, 0, 0);
  position: absolute;
  transition: 0.8s;
  pointer-events: none;
  width: 100vw;
  height: 100vh;
  z-index: 4;
  top: 0;
  left: 0;
}

.help-active{
  pointer-events: all;
  background-color: rgba(0, 0, 0, 0.377);
}

.help-grid-cell{
  width: 50px;
  height: 50px;
  display: flex;
  align-items: center;
  justify-content: center;
  border: 1px solid black;
  background-color: rgb(243, 242, 181);
}

.help-nav-grid{
  margin: auto;
  display: grid;
  position: relative;
  width: max-content;
  height: max-content;
  grid-template-columns: repeat(3, 50px);
}

.help-dialogue-box{
  position: absolute;
  top: 50%;
  right: 50%;
  background: linear-gradient(50deg, rgb(239, 196, 245), rgb(204, 193, 204));
  box-shadow: 0 0 10px rgb(19, 18, 18);
  width: 75vw;
  height: 80vh;
  overflow: hidden;
  transform: translate(50%, -50%) scale(1, 1);
  border-radius: 5px;
  color: rgb(36, 33, 33);
  user-select: none;
  border-radius: 5px;
}

.help-dialogue-box>div{
  padding: 20px;
}

.help-dialogue-box div::-webkit-scrollbar{
  background-color: rgba(105, 105, 105, 0.2);
  width: 8px;
}

.help-dialogue-box div::-webkit-scrollbar-thumb{
  border-radius: 50px;
  background-color: rgb(105, 106, 107);
}

.help-open{
  animation-name: expand;
  animation-duration: 0.6s;
  animation-direction: normal;
  animation-fill-mode: forwards;
}

.help-close{
  animation-name: expand;
  animation-duration: 0.6s;
  animation-direction: reverse;
  animation-fill-mode: forwards;
}

@keyframes expand{
  0%{
    transform: translate(50%, -50%) scale(0.8, 0.8);
    opacity: 0;
  }

  100%{
    opacity: 1;
    transform: translate(50%, -50%) scale(1, 1);
  }
}

</style>