<template>
  <div id="app">
    <leaflet-map></leaflet-map>
  </div>
</template>

<script>
  import LeafletMap from './components/LeafletMap'

export default {
  name: 'App',
  components: {
      LeafletMap
  }
}
</script>

<style>
  html, body, #app {
    height: 100vh;
    width: 100vw;
    margin: 0;
    padding: 0;
  }
</style>
