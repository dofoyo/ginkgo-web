<template>
  <div class="container">
    <div class="panel panel-default">
      <div class="panel-heading">
        <button @click="isshowprojectview=!isshowprojectview">创建项目</button>
      </div>
      <div class="panel-body" v-show="isshowprojectview">
          <div class="form-group">
            <label>名称</label>
            <input type="text" v-model="projectname" />
          </div>

          <div class="form-group">
            <label>描述</label>
            <input type="text" v-model="description" />
          </div>

          <div class="form-group">
            <label></label>
            <button @click="saveProject">创建</button>
          </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "projectview",
  data () {
    return {
        projectname:'',
        description:'',
        isshowprojectview:false
    }
  },
  mounted: function() {
      this.getData();
  },
  methods:{
    getData:function(){

    },
    saveProject:function(){
      var apiurl = 'http://localhost:8081/project'
      var resource = this.$resource(apiurl);
      var vm = this;
      var project = {projectname:vm.projectname,description:vm.description};
      //console.log(project);
      resource.update({id:''},project)
              .then((respones) =>{ 
                  console.log(response);
                })
              .catch(function(response){
                console.log("there are something wrong!!!");
                console.log(response);
              });
      this.isshowprojectview = false;
      this.projectname = "";
      this.description = "";

      history.go(0);

      
    }
  }
}
</script>

<style>

</style>
