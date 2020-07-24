<template>
  <div id="app" @click="isTimeout">
    <!-- <img src="./assets/logo.png"> -->
    <router-view/>
  </div>
</template>

<script>
export default {
  name: 'App',
  data(){
    return{
      lastTime: new Date().getTime(), // 最后一次点击的时间
      curTime: new Date().getTime(), //当前时间
      timeOut: 3 * 1000,
      t1: ''
    }
  },
  mounted(){
    this.t1 = setInterval(this.checkTime, 1000 * 60 * 10);
  },
  methods:{
    isTimeout(){
      this.lastTime = new Date().getTime()
    },
    checkTime(){
      if(localStorage.getItem('loginname')!='undefined'){
        this.curTime = new Date().getTime()
        if(this.curTime-this.lastTime>this.timeOut){
          //未登录
          if(localStorage.getItem('loginname')==undefined){
            this.lastTime = new Date().getTime()
          }else {
            //已登录
            this.$message({
              type:'info',
              message:'登录超时，请重新登录!',
              duration:2500
            })
            setTimeout({},5000)
            this.$router.push('/')
          }
        }
      }
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
body{
    font-family: "微软雅黑";  /*  设置字体 */
    margin: 0px auto /* 去除上下的边距*/
  }
</style>
