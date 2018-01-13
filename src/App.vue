<template>
  <div id="app">
    <StopTime/>
  </div>
</template>

<script>
import StopTime from './components/StopTime'

export default {
  name: 'app',
  components: {
    StopTime
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
</style>
