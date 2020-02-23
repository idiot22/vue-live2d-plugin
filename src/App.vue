<template>
  <div id="app">
    <live2d/>
  </div>
</template>

<script>
import Live2d from './components/live2d/live2d'

export default {
  name: 'App',
  components: {
    Live2d
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
