<template>
  <div class="container-fluid">
    <div class="panel panel-primary">
      <div class="panel-heading">
         <table width="100%">
           <tr>
             <td>
                <h2 class="panel-title">{{board.boardname}}</h2>
             </td>
              <td width="10%" align="center"> 
                <small @click="refreshBoardAndProject">刷新</small>
             </td>              
             <td width="30%" align="center">
                <small v-show="loading">读取中....</small>
                <small v-show="refreshing">刷新中....</small>
                <small v-show="updating">更新中....</small>
             </td>
           </tr>
         </table>

      </div>
      <div class="panel-body">
        <div class="col-md-2" v-for="stage in board.stages">
          <p>{{stage.stagename}}</p>
          <draggable element="ul" class="list-group" v-model="stage.projects" :id="stage.stageid" :options="{group:'projects'}" @add="addDrag" @end="endDrag"> 
            <li class="list-group-item" v-for="project in stage.projects" :id="project.projectid"> 
              <span @click='setProjectType(project.projectid)' :class="project.typeclass" class="glyphicon glyphicon-unchecked"></span>

              &nbsp&nbsp

              <span @click='selectProject(project.projectid)':class="project.typeclass">{{project.projectname}}</span>

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
      projectname:'',
      loading:false,
      refreshing:false,
      updating:false
    }
  },
  mounted: function() {
      this.getData();
  },
  methods:{
    getData:function(){
      var root = process.env.API_ROOT;
      this.loading = true;
      var apiurl = root + 'board';
      var resource = this.$resource(apiurl);
      var vm = this;
      resource.get()
              .then((response) => {
                //console.log(response.data.content.stages);
                vm.$set(this.board,'boardid',response.data.content.bordid);
                vm.$set(this.board,'boardname',response.data.content.boardname);
                vm.$set(this.board,'stages',response.data.content.stages);

                for (var i = response.data.content.stages.length - 1; i >= 0; i--) {
                   var stage = response.data.content.stages[i];
                   for (var j = stage.projects.length - 1; j >= 0; j--) {
                     var project = stage.projects[j];                    
                      switch(project.type){
                        case 0:
                          project.typeclass = "typezero";
                          break;
                        case 1:
                          project.typeclass = "typeone";
                          break;
                        case 2:
                          project.typeclass = "typetwo";
                          break;
                        case 3:
                          project.typeclass = "typethree";
                          break;
                        case 4:
                          project.typeclass = "typefour";
                          break;
                        case 5:
                          project.typeclass = "typefive";
                          break;
                        case 6:
                          project.typeclass = "typesix";
                          break;
                      }
                   }
                 } 

                this.loading = false;
              })
              .catch(function(response) {
                console.log("there are something wrong!!!");
                console.log(response);
                this.loading = false;
              })
    },
    refreshBoardAndProject:function(){
      var root = process.env.API_ROOT;
      this.refreshing = true;
      var apiurl = root + 'refreshBoardAndProject';
      var resource = this.$resource(apiurl)
      var vm = this;
      resource.get()
              .then((response) => {
                //console.log(response.data.content.stages);
                vm.$set(this.board,'boardid',response.data.content.bordid);
                vm.$set(this.board,'boardname',response.data.content.boardname);
                vm.$set(this.board,'stages',response.data.content.stages);
                this.refreshing = false;
              })
              .catch(function(response) {
                console.log("there are something wrong!!!");
                console.log(response);
                this.refreshing = false;
              })
    },
    updateData:function(stageid,projects){
      var root = process.env.API_ROOT;
      this.updating = true;
      var apiurl = root + 'stage'
      var resource = this.$resource(apiurl);
      var vm = this;
      resource.update({id:stageid},projects)
              .then((respones) =>{ 
                  //console.log(response);
                  this.updating = false;
                })
              .catch(function(response){
                console.log("there are something wrong!!!");
                console.log(response);
                this.updating = false;
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
            //console.log(stageid +':'+ JSON.stringify(this.board.stages[i].projects));
            //console.log(evt);
            break;
        }
      }
    },
    selectProject:function(projectid){
      this.$emit('selectedProjectid',projectid);

    },
    setProjectType:function(projectid){
      this.updateProjectType(projectid);
     // console.log("projectid = " + projectid);

      var stage,project;
      var flag = false;
      for(var i=0; i<this.board.stages.length; i++){
        stage = this.board.stages[i];
        for(var j=0; j<stage.projects.length; j++){
          project = stage.projects[j];
          //console.log(" --   " + project.projectid);
          if(project.projectid == projectid){
            //console.log("find one");
            //console.log(project)
            flag = true;
          }
          if(flag){
            break;
          }
        }
        if(flag){
            break;
        }
      }

      if(flag){      
        project.type==6 ? project.type=0 : project.type=project.type+1;
        switch(project.type){
          case 0:
            project.typeclass = "typezero";
            break;
          case 1:
            project.typeclass = "typeone";
            break;
          case 2:
            project.typeclass = "typetwo";
            break;
          case 3:
            project.typeclass = "typethree";
            break;
          case 4:
            project.typeclass = "typefour";
            break;
          case 5:
            project.typeclass = "typefive";
            break;
          case 6:
            project.typeclass = "typesix";
            break;
        }



        //console.log("change type.");
        //console.log(project);        
      }else{
        //console.log("can NOT find it.");
      }

    },
    updateProjectType:function(projectid){
      var root = process.env.API_ROOT;
      this.updating = true;
      var apiurl = root + 'projecttype'
      var resource = this.$resource(apiurl);
      var vm = this;
      resource.update({id:projectid},{})
              .then((respones) =>{ 
                  //console.log(response);
                  this.updating = false;
                })
              .catch(function(response){
                console.log("there are something wrong!!!");
                console.log(response);
                this.updating = false;
              });
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

  .typezero{
    color:black;
  }
  .typeone{
    color:red;
  }
  .typetwo{
    color:orange;
  }
  .typethree{
    color:yellow;
  }
  .typefour{
    color:green;
  }
  .typefive{
    color:blue;
  }
  .typesix{
    color:purple;
  }
  
</style>
