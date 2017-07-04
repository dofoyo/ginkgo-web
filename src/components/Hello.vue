<template>
  <div class="container-fluid">
    <div class="panel panel-default">
      <div class="panel-heading">
        <h3 class="panel-title">{{board.boardname}}</h3>
      </div>
      <div class="panel-body">
        <div class="col-md-2" v-for="stage in board.stages">
          <p>{{stage.stagename}}</p>
          <draggable element="ul" class="list-group" v-model="stage.tasks" :id="stage.stageid" :options="{group:'tasks'}" @add="addDrag" @end="endDrag"> 
                <li class="list-group-item" v-for="task in stage.tasks"> 
                    {{task.taskname}}<br>{{task.description}}
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
  name: "hello",
  components: {
    draggable
  },
  data () {
    return {
      board:{}
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
                vm.$set(this.board,'boardid',response.data.content.bordid);
                vm.$set(this.board,'boardname',response.data.content.boardname);
                vm.$set(this.board,'stages',response.data.content.stages);
              })
              .catch(function(response) {
                console.log("there are something wrong!!!");
                console.log(response);
              })
    },
    updateData:function(stageid,tasks){
      var apiurl = 'http://localhost:8081/stage'
      var resource = this.$resource(apiurl);
      var vm = this;
      resource.update({id:stageid},tasks)
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
            this.updateData(stageid,this.board.stages[i].tasks);
            break;
        }
      }
    },
    endDrag: function (evt) {
      var stageid = evt.from.id;
      for(var i=0; i<this.board.stages.length; i++){
        if(this.board.stages[i].stageid == stageid){
            this.updateData(stageid,this.board.stages[i].tasks);
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
