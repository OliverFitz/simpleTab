<template>
  <div id="app">
    <HelloWorld/>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'app',
  components: {
    HelloWorld
  }
}
</script>

<style>
html, body{
  width: 100%;
  height: 100%;
  -webkit-user-select:none
}
*{
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}
#app {
  width: 100%;
  height: 100%;
}
</style>
