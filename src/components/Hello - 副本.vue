<template>
  <div class="container-fluid">
    <div class="panel panel-default">
      <div class="panel-heading">
        <h3 class="panel-title">{{boardname}}</h3>
      </div>
      <div class="panel-body">
        <div class="col-md-2" v-for="stage in stages">
          <p>{{stage.stagename}}</p>
          <draggable element="ul" class="list-group" v-model="stage.tasks" :id="stage.stageid" :options="{group:'tasks'}" @add="addDrag" @end="endDrag"> 
                <li class="list-group-item" v-for="task in stage.tasks"> 
                  <div :id="task.taskid">
                    {{task.taskname}}<br>{{task.description}}
                  </div>
                </li> 
          </draggable>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import draggable from "vuedraggable"

export default {
  name: "hello",
  components: {
    draggable
  },
  data () {
    return {
      boardid:'',
      boardname:'',
      stages:[]
    }
  },
  mounted: function() {
      this.getData();
  },
  methods:{
    getData:function(){
      this.boardid = "001";
      this.boardname = "IT系统建设看板";
      this.stages =[{"stageid":"stage0","stagename":"1.需求"},
                    {"stageid":"stage1","stagename":"2.方案"},
                    {"stageid":"stage2","stagename":"3.设计"},
                    {"stageid":"stage3","stagename":"4.开发与测试"},
                    {"stageid":"stage4","stagename":"5.应用"},
                    {"stageid":"stage5","stagename":"6.验收"}
                    ];
      this.$set(this.stages[0],"tasks",[{'taskname':'dog','taskid':0,'description':'my best friend in the world'},
                                        {'taskname':'cat','taskid':1,'description':'it is black'},
                                        {'taskname':'hen','taskid':2,'description':'give me eggs every day'},
                                        {'taskname':'mouse','taskid':3,'description':'i hate it'}
                                        ]);
      this.$set(this.stages[1],"tasks",[]);
      this.$set(this.stages[2],"tasks",[{'taskname':'bird','taskid':4,'description':'she can fly'},
                                        {'taskname':'eargle','taskid':5,'description':'please catch the mouse'},
                                        {'taskname':'tigger','taskid':6,'description':'it is so terrible'}
                                        ]);
      this.$set(this.stages[3],"tasks",[]);
      this.$set(this.stages[4],"tasks",[]);
      this.$set(this.stages[5],"tasks",[]);
    },
    addDrag: function (evt) {
      console.log("addDrag");
      //console.log(evt);
      //var taskid = evt.to.innerHTML.match(/<div id="(\S*)">/)[1];
      var stageid = evt.to.id;
      //var list = evt.to.innerHTML.match(/id="(\S*)"/g);
      //console.log('stage:' + stageid + ', list: ' + list);

      var index = stageid.substring(5,stageid.length);
      console.log(stageid +':'+ JSON.stringify(this.stages[index].tasks));
    },
    endDrag: function (evt) {
      console.log("endDrag");
      //console.log(evt);
      //var taskid = evt.to.innerHTML.match(/<div id="(\S*)">/)[1];
      var stageid = evt.from.id;
      //var list = evt.to.innerHTML.match(/id="(\S*)"/g);
      //console.log('stage:' + stageid + ', list: ' + list);

      var index = stageid.substring(5,stageid.length);
      console.log(stageid +':'+ JSON.stringify(this.stages[index].tasks));
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
