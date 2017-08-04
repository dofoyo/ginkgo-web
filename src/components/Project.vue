<template>
  <div class="container-fluid">
    <div class="panel panel-primary">
      <div class="panel-heading">

         <table width="100%">
           <tr>
             <td>
                <h2 class="panel-title">{{projectname}}</h2>
             </td>
             <td>
                <small @click="showPerson=!showPerson">显示参与人</small>
             </td>

             <td width="300px" align="right">
                <div class="input-group">
                  <input type="text" class="form-control" placeholder="输入任务编号" aria-describedby="basic-addon2" v-model="taskid">
                  <span class="input-group-btn">
                    <button class="btn btn-default" type="button" @click="addTask">加入任务</button>
                  </span>
                </div>
             </td>
           </tr>
         </table>
      </div>
      <p></p>
      <div class="row">
        <div class="col-sm-5 col-md-3" v-for="task in tasks">
          <div class="panel panel-info">
            <div class="panel-heading">
              <table width="100%">
                <tr>
                  <td>
                    <span @click="collapseTaskdetail(task.taskid)">V&nbsp</span>
                  </td>
                  <td>
                    <h5 class="panel-title">
                        <span>{{task.subject}}</span>
                    </h5>
                  </td>

                  <td>
                        <span @click="removeTask(task.taskid)"> X </span>                    
                  </td>
                </tr>
              </table>
              
              <div v-show="showPerson">
                <div>
                  执行人：
                  <span v-for="actor in task.actors">{{actor}}, </span>
                </div>
                <div>
                  知会人：
                  <span v-for="other in task.others">{{other}}, </span>
                </div>
              </div>

            </div>
            <div class="panel-body" :id='task.taskid'>
              <li v-for="taskDetail in task.taskDetail">
                <strong>{{taskDetail.person}}</strong>&nbsp&nbsp<small>{{taskDetail.dateAndTime}}</small>
                <div v-html='taskDetail.done'></div><br>
              </li>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "project",
  props:['showid'],
  data () {
    return {
      projectid:'',
      projectname:'',
      description:'',
      tasks:[],
      taskid:'',
      showPerson:false
    }
  },
  watch:{
    showid:function() {
      this.getData(this.showid);
    }
  },
  methods:{
    getData:function(showid){
      var apiurl = 'http://localhost:8081/projects';
      var resource = this.$resource(apiurl)
      var vm = this;
      resource.get({id:showid})
              .then((response) => {
                this.projectid = response.data.content.projectid;
                this.projectname = response.data.content.projectname;
                this.description = response.data.content.description;
                this.tasks = response.data.content.tasks;
              })
              .catch(function(response) {
                console.log("there are something wrong!!!");
                console.log(response);
              })
    },
    addTask:function(){
      var apiurl = 'http://localhost:8081/task'
      var resource = this.$resource(apiurl);
      var vm = this;
      var projectid_taskid = vm.showid + "," + vm.taskid;
      var ptid = {"ptid":projectid_taskid}
      //console.log(projectid_taskid);
      resource.update({id:projectid_taskid},ptid)
              .then((respones) =>{ 
                  console.log(response);
                  this.getData(this.showid); 
                })
              .catch(function(response){
                console.log("addTask Error !!!");
                console.log(response);
                this.getData(this.showid); 
              });
      this.taskid = "";      
    },
    collapseTaskdetail:function(taskid){
         var t = document.getElementById(taskid);
         if(t.style.display == "none"){
            t.style.display = "block";
         }else{
            t.style.display = "none";
         }
    },
    removeTask:function(taskid){
      var isremove = confirm("Are you sure?");
      if(isremove){
        var apiurl = 'http://localhost:8081/task'
        var resource = this.$resource(apiurl);
        var vm = this;
        var projectid_taskid = vm.showid + "," + taskid;
        var ptid = {"ptid":projectid_taskid}
        console.log(projectid_taskid);
        resource.remove({id:projectid_taskid},ptid)
                .then((respones) =>{ 
                    console.log(response);
                    this.getData(this.showid); 
                  })
                .catch(function(response){
                  console.log("removeTask Error !!!");
                  console.log(response);
                  this.getData(this.showid); 
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
