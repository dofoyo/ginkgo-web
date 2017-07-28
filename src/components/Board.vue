<template>
  <div class="container-fluid">
    <div class="panel panel-default">
      <div class="panel-heading">
         <h3 class="panel-title">{{board.boardname}}</h3>
      </div>
      <div class="panel-body">
        <div class="col-md-2" v-for="stage in board.stages">
          <p>{{stage.stagename}}</p>
          <draggable element="ul" class="list-group" v-model="stage.projects" :id="stage.stageid" :options="{group:'projects'}" @add="addDrag" @end="endDrag"> 
                <li class="list-group-item" v-for="project in stage.projects"> 
                    {{project.projectname}}<br>{{project.description}}
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
      board:{}
    }
  },
  props: ['isshowprojectview'],
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
            break;
        }
      }
    },
    endDrag: function (evt) {
      var stageid = evt.from.id;
      for(var i=0; i<this.board.stages.length; i++){
        if(this.board.stages[i].stageid == stageid){
            this.updateData(stageid,this.board.stages[i].projects);
            break;
        }
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
