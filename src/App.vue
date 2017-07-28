<template>
  <div id="app">
    <board></board>
    <projectview></projectview>
    <div class="logo">
       <img src="./assets/logo.png">
    </div> 
  </div>
</template>

<script>
import board from './components/Board'
import projectview from './components/Projectview'

export default {
  name: 'app',
  components: {
    board,
    projectview
  },
  data () {
    return {
      
    }
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
