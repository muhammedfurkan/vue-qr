<template>
  <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
  <div style="margin-bottom: 20px; font-size: 20px; font-weight: bold; color: #2c3e50; text-align: center; margin-top: 20px; margin-bottom: 20px;">
    Offismekan QR Code Scanner
  </div>
  <QrcodeStream />
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import QrcodeStream from './components/QrcodeStream.vue'


export default {
  name: 'App',
  components: {
    QrcodeStream
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
