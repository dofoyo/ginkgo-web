<template>
  <div id="app">
    <hello></hello>
    <div class="logo">
       <img src="./assets/logo.png">
    </div> 
  </div>
</template>

<script>
import Hello from './components/Hello'

export default {
  name: 'app',
  components: {
    Hello
  }
}
</script>

<style>

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 10px;
}

#app .logo{
  text-align: center;
}
</style>
