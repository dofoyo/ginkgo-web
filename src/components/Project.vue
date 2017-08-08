<template>
  <div class="container-fluid">
    <div class="panel panel-primary">
      <div class="panel-heading">
         <table width="100%">
           <tr>
             <td>
                <h2 class="panel-title">{{projectname}}:</h2>
             </td>
             <td>
              <input type="text" class="form-control" v-model="showid" readonly="true">
             </td>
              <td width="10%" align="center"> 
                <small @click="refreshProjectAndTask">刷新</small>
             </td>              
             <td width="30%" align="center">
                <small v-show="loading">读取中....</small>
                <small v-show="refreshing">刷新中....</small>
             </td>
             <td aligh="right">
                <small @click="showPerson=!showPerson">显示参与人</small>
             </td>
           </tr>
         </table>
      </div>
      <p></p>
      <div class="row">
        <div class="col-md-2" v-for="task in tasks">
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
      showPerson:false,
      loading:false,
      refreshing:false
    }
  },
  watch:{
    showid:function() {
      this.getData(this.showid);
    }
  },
  methods:{
    getData:function(showid){
      var root = process.env.API_ROOT;
      this.loading = true;
      var apiurl = root + 'project';
      console.log("apiurl");
      console.log(apiurl);
      var resource = this.$resource(apiurl)
      var vm = this;
      resource.get({id:showid})
              .then((response) => {
                this.projectid = response.data.content.projectid;
                this.projectname = response.data.content.projectname;
                this.description = response.data.content.description;
                this.tasks = response.data.content.tasks;
                this.loading = false;
              })
              .catch(function(response) {
                console.log("there are something wrong!!!");
                console.log(response);
                this.loading = false;
              })
    },
    collapseTaskdetail:function(taskid){
         var t = document.getElementById(taskid);
         if(t.style.display == "none"){
            t.style.display = "block";
         }else{
            t.style.display = "none";
         }
    },
    refreshProjectAndTask:function(){
      var root = process.env.API_ROOT;
      this.refreshing = true;
      var apiurl = root + 'refreshProjectAndTask';
      var resource = this.$resource(apiurl)
      var vm = this;
      resource.get({id:vm.showid})
              .then((response) => {
                this.projectid = response.data.content.projectid;
                this.projectname = response.data.content.projectname;
                this.description = response.data.content.description;
                this.tasks = response.data.content.tasks;
                this.refreshing = false;
              })
              .catch(function(response) {
                console.log("there are something wrong!!!");
                console.log(response);
                this.refreshing = false;
              })
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
