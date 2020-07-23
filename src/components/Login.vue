<template>
  <div class="login-container">
      <el-form style="display:none">
            <el-row :gutter="5">
              <el-col :span="4">
                <el-input placeholder="input username" v-model="username" name="username" id="username" >
                  <i slot="prefix" class="el-input__icon el-icon-user"></i>
                </el-input>
              </el-col>
            </el-row>
            <el-row :gutter="5">
              <el-col :span="4">
                <el-input placeholder="input password" v-model="password" name="password" id="password" >
                  <i slot="prefix" class="el-input__icon el-icon-lock"></i>
                </el-input>
              </el-col>
            </el-row>
            <el-row :gutter="5">
              <el-col :span="2">
                <el-button type="primary" @click="register()">注册</el-button>
              </el-col>
              <el-col :span="2">
                <el-button type="success" @click="login()">登录</el-button>
              </el-col>
            </el-row>
      </el-form>

      <el-form ref="form" :model="form" label-width="80px" class="login-form">
        <h2 class="login-title">大数据可视化系统</h2>
        <el-form-item label="用户名">
          <el-input placeholder="input username" v-model="form.username"  id="username" >
            <i slot="prefix" class="el-input__icon el-icon-user"></i>
          </el-input>
        </el-form-item>
        <el-form-item label="密码">
          <el-input show-password placeholder="input password" v-model="form.password"   id="password" >
            <i slot="prefix" class="el-input__icon el-icon-lock"></i>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-row>
            <!-- <el-col :span="8">
              <i class="el-icon-warning-outline">忘记密码</i>
            </el-col> -->
            <el-col :span="8">
              <el-button type="success" @click="register">注册</el-button>
            </el-col>
            <el-col :span="8">
              <el-button type="primary" @click="login">登录</el-button>
            </el-col>
            
          </el-row>
        </el-form-item>
      </el-form>
  </div>
</template>

<script>
export default {
  name: 'Login',
  data () {
    return {
      form:{
        username:'',
        password:''
      },
      msg: 'Welcome to My Bigdata System'
    }
  },
  methods:{
    register(){

    },
    login(){
      this.$axios.post('/tovalid',{
        ...this.form
      },'json')
      .then(result => {
        console.log("result:"+JSON.stringify(result,null,2))
      })
      .catch(exp => {
        console.log(exp)
      })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.el-row{margin-bottom:20px}

/* 背景 */
.login-container {
  position: absolute;
  width: 100%;
  height: 100%;
  background: url("../assets/loginbackground.jpg");
  background-repeat: no-repeat;
}

.login-form {
  width: 350px;
  margin: 160px auto; /* 上下间距160px，左右自动居中*/
  background-color: rgb(255, 255, 255, 0.8); /* 透明背景色 */
  padding: 30px;
  border-radius: 20px; /* 圆角 */
}


/* 标题 */
.login-title {
  color: #303133;
  text-align: center;
}
</style>
