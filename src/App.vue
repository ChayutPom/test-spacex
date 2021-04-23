<template>
  <div id="app">
    <spacex />
  </div>
</template>

<script>
import spacex from './components/spacex.vue'

export default {
  name: 'App',
  components: {
    spacex
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
