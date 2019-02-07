<template>
  <div id="app">
    <DrawingBoard/>
  </div>
</template>
<script>
import DrawingBoard from './components/DrawingBoard.vue'

export default {
  name: 'app',
  components: {
    DrawingBoard
  }
}
</script>

<style>
body {
  margin: 0;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
</style>
