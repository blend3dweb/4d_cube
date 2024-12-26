<template>
  <div id="app">
    <h1>4D Cube (Tesseract) Visualization</h1>
    <FourDCube />
  </div>
</template>

<script>
import FourDCube from './components/FourDCube.vue'

export default {
  name: 'App',
  components: {
    FourDCube
  }
}
</script>

<style>
#app {
  font-family: Arial, sans-serif;
  text-align: center;
  margin-top: 20px;
}
</style>

