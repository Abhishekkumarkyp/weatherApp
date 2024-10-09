<template>
  <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
  <WeatherInformation/>
  <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import WeatherInformation from './components/WeatherInformation copy 3.vue'

export default {
  name: 'App',
  components: {
    // HelloWorld,
    WeatherInformation
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
