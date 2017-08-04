<template>
  <div class="container-fluid">
    <div class="panel panel-primary">
      <div class="panel-heading">
         <table width="100%">
           <tr>
             <td>
                <h2 class="panel-title">{{board.boardname}}</h2>
             </td>
             <td width="300px" align="right">
                <div class="input-group">
                  <input type="text" class="form-control" placeholder="输入项目名" aria-describedby="basic-addon2" v-model="projectname">
                  <span class="input-group-btn">
                    <button class="btn btn-default" type="button" @click="createProject">创建项目</button>
                  </span>
                </div>
             </td>
           </tr>
         </table>
      </div>
      <div class="panel-body">
        <div class="col-md-2" v-for="stage in board.stages">
          <p>{{stage.stagename}}</p>
          <draggable element="ul" class="list-group" v-model="stage.projects" :id="stage.stageid" :options="{group:'projects'}" @add="addDrag" @end="endDrag"> 
                <li class="list-group-item" v-for="project in stage.projects" :id="project.projectid"> 
                    <table width="100%">
                      <tr>
                        <td>
                          <span @click='selectProject(project.projectid)'>{{project.projectname}}</span>
                        </td>
                        <td align="right">
                          <span @click="removeProject(project.projectid)"> X </span>                 
                        </td>
                      </tr>
                    </table>
                </li> 
          </draggable>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import draggable from "vuedraggable";

export default {
  name: "board",
  components: {
    draggable
  },
  data () {
    return {
      board:{},
      projectname:''
    }
  },
  mounted: function() {
      this.getData();
  },
  methods:{
    getData:function(){
      var apiurl = 'http://localhost:8081/board';
      var resource = this.$resource(apiurl)
      var vm = this;
      resource.get()
              .then((response) => {
                console.log(response.data.content.stages);
                vm.$set(this.board,'boardid',response.data.content.bordid);
                vm.$set(this.board,'boardname',response.data.content.boardname);
                vm.$set(this.board,'stages',response.data.content.stages);
              })
              .catch(function(response) {
                console.log("there are something wrong!!!");
                console.log(response);
              })
    },
    updateData:function(stageid,projects){
      var apiurl = 'http://localhost:8081/stage'
      var resource = this.$resource(apiurl);
      var vm = this;
      resource.update({id:stageid},projects)
              .then((respones) =>{ 
                  console.log(response);
                })
              .catch(function(response){
                console.log("there are something wrong!!!");
                console.log(response);
              });
    },
    addDrag: function (evt) {
      var stageid = evt.to.id;
      for(var i=0; i<this.board.stages.length; i++){
        if(this.board.stages[i].stageid == stageid){
            this.updateData(stageid,this.board.stages[i].projects);
            //console.log(stageid +':'+ JSON.stringify(this.board.stages[i].projects));
            //console.log(evt);
            break;
        }
      }
    },
    endDrag: function (evt) {
      var stageid = evt.from.id;
      for(var i=0; i<this.board.stages.length; i++){
        if(this.board.stages[i].stageid == stageid){
            this.updateData(stageid,this.board.stages[i].projects);
            console.log(stageid +':'+ JSON.stringify(this.board.stages[i].projects));
            console.log(evt);
            break;
        }
      }
    },
    createProject:function(){
      var apiurl = 'http://localhost:8081/project'
      var resource = this.$resource(apiurl);
      var vm = this;
      var project = {projectname:vm.projectname};
      //console.log(project);
      resource.update({id:''},project)
              .then((respones) =>{ 
                  console.log(response);
                   this.getData();
                })
              .catch(function(response){
                console.log("there are something wrong!!!");
                console.log(response);
                 this.getData();
              });
      this.projectname = "";
      
    },
    selectProject:function(projectid){
      this.$emit('selectedProjectid',projectid);

    },
    removeProject:function(projectid){
      var isremove = confirm("Are you sure?");
      if(isremove){
        var apiurl = 'http://localhost:8081/project'
        var resource = this.$resource(apiurl);
        var vm = this;
        var ptid = {"ptid":projectid}
        resource.remove({id:projectid},ptid)
                .then((respones) =>{ 
                    console.log(response);
                    this.getData(); 
                  })
                .catch(function(response){
                  console.log("remove project Error !!!");
                  console.log(response);
                  this.getData(); 
                });   
      }
    }
  }
}
</script>

<style>
  .list-group {
    min-height:20px;
  }

  .list-group-item {
    cursor: move;
  }
</style>
