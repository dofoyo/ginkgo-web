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
            <li class="list-group-item" v-for="project in stage.projects" :id="project.projectid" @click='selectProject(project.projectid)'> 
              {{project.projectname}}
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
                console.log(response.data.content.stages);
                vm.$set(this.board,'boardid',response.data.content.bordid);
                vm.$set(this.board,'boardname',response.data.content.boardname);
                vm.$set(this.board,'stages',response.data.content.stages);
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
                console.log(response.data.content.stages);
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
                  console.log(response);
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
            console.log(stageid +':'+ JSON.stringify(this.board.stages[i].projects));
            console.log(evt);
            break;
        }
      }
    },
    selectProject:function(projectid){
      this.$emit('selectedProjectid',projectid);

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
