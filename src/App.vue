<template>
  <div id="app">
  <wx-audio :source="audio"></wx-audio>
  </div>
</template>

<script>
import WxAudio from './components/Audio.vue'
export default {
  name: 'app',
  data(){
    return{
      audio:{
        src:"http://qiniu.g-easy.com.cn/09%20Wildest%20Dreams.mp3",
        name:"Wildest Dream.mp3"
      }
    }
  },
  components:{
    WxAudio
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
*{
  box-sizing: border-box;
}
</style>
