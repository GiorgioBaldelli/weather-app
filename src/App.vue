<template>
  <div id="app">
    <weather></weather>
  </div>
</template>

<script>

import Weather from './components/weather.vue'

export default {
  name: 'app',
  components: {
    Weather
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
}
</style>
