<template>
  <div id="app">
    <WeatherApp></WeatherApp>
  </div>
</template>

<script>
import WeatherApp from './components/WeatherApp.vue'

export default {
  name: 'Vueather',
  components: {
    WeatherApp: WeatherApp,
  }
}
</script>

<style>

</style>