<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <QrcodeStream @decode="onDecode"></QrcodeStream>
</template>

<script>

import { QrcodeStream } from 'vue-qrcode-reader'


export default {
  name: 'App',
  components: {
    QrcodeStream,
  },
  data(){
    return{}
  },
  methods:{
    onDecode(log){
      console.log(log)
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
