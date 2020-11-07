<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <div class="nale ">
        <div class="title">sqlite3数据库的数据</div>
        <template v-for="(item, i) in list">
          <div :key="i">姓名：{{ item.name }}</div>
        </template>
      </div>
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'
import { ipcRenderer } from 'electron'
import { remote } from 'electron'
export default {
  name: 'Home',
  components: {
    HelloWorld
  },
  data(){
    return{
      list: [],
    }
  },
  created(){
    window.ipcRenderer = ipcRenderer
    //sqlite3测试
   var sqlite3 = require('sqlite3').verbose();
   console.log("sqlite3测试：",sqlite3)
      const path = require("path");
      let _that = this;
       let lsrc=path.join(__resources, "/data/local.db")
      console.log("路径：",lsrc)
      // let ruing=path.join(remote.app.getPath('userData'), '/data.db');
     const db = new sqlite3.Database(lsrc);
      db.run("create table test( id INTEGER PRIMARY KEY AUTOINCREMENT,name varchar(15))", function() {
      let addname="测名称："+_that.datet();
      db.run('insert into test (name) values("'+addname+'")', function() {
        db.all("select * from test order by id desc limit 8 ", function(err, res) {
          if (!err) {
            _that.list=res
            console.log(JSON.stringify(res));
          } else {
            console.log(err);
          }
        });
      });
    });


  },
  methods:{
     datet(){
       const date = new Date();
      const Y = date.getFullYear() + '-';
      const M = (date.getMonth() + 1 < 10 ? '0' + (date.getMonth() + 1) : date.getMonth() + 1) + '-';
      const D = date.getDate() + ' ';
      const h = date.getHours() + ':';
      const m = date.getMinutes();
      const dateString = Y + M + D + h + m;
      return dateString;
    }
  }
}
</script>
