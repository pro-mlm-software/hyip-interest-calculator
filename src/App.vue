<template>
  <div id="app">
   <div style="background-color:#2a31a3">
      <div class="container mx-auto py-4">
          <a href="https://phphyipmanagerscript.com/hyip-calculator/" target="_blank">
            <img src="./assets/php_hyip_manager_script_logo.png" class="h-12">
          </a>
      </div>
   </div>
    <router-view/>
  </div>
</template>

<script>
export default {
  name: 'App'
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
