<template>
  <div id="app">
    <sku-module></sku-module>
  </div>
</template>

<script>
import skuModule from './components/SkuModule'

export default {
  name: 'app',
  components: {
    skuModule
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
