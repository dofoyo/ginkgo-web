<template>
  <div id="app">
    <board v-on:selectedProjectid='showNewProject'></board>
    <project :showid='showid'></project>
    <div class="logo">
       <img src="./assets/logo.png">
    </div> 
  </div>
</template>

<script>
import board from './components/Board'
import project from './components/Project'

export default {
  name: 'app',
  components: {
    board,
    project
  },
  data () {
    return {
      showid:''
    }
  },
  methods:{
    showNewProject:function(id){
      this.showid = id;
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
