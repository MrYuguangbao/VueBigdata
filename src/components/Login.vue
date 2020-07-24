<template>
  <div class="login-container">
    <div id="contPar" class="contPar">
        <div id="page1" style="z-index:1;">
            <div class="imgGroug">
                <ul>
                    <img alt="" class="img0 png" src="../assets/page1_0.png">
                    <img alt="" class="img1 png" src="../assets/page1_1.png">
                    <img alt="" class="img2 png" src="../assets/page1_2.png">
                </ul>
            </div>
            <img alt="" class="img3 png" src="../assets/page1_3.jpg">
        </div>
    </div>

      <!-- <el-form ref="form" :rules="rules" :model="form" label-width="80px" class="login-form">
        <h2 class="login-title">大数据可视化系统</h2>
        <el-form-item label="用户名" prop="username">
          <el-input placeholder="用户名" v-model="form.username"  id="username" >
            <i slot="prefix" class="el-input__icon el-icon-user"></i>
          </el-input>
        </el-form-item>
        <el-form-item label="密码" prop="password">
          <el-input show-password placeholder="密码" v-model="form.password"   id="password" >
            <i slot="prefix" class="el-input__icon el-icon-lock"></i>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="login('form')">登录</el-button>
        </el-form-item>
      </el-form> -->

      <el-form ref="form" :rules="rules" :model="form" label-width="0px" class="login-form">
        <el-row>
          <el-col :span="22">
            <el-form-item><label style="font-size:23px">大数据可视化系统</label></el-form-item>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="22">
            <el-form-item  prop="username">
              <el-input placeholder="用户名" style="width:100%" v-model="form.username"  id="username" >
                <i slot="prefix" class="el-icon-user"></i>
              </el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="22">
            <el-form-item  prop="password">
              <el-input show-password placeholder="密码" style="width:100%" v-model="form.password"   id="password" >
                <i slot="prefix" class="el-icon-lock"></i>
              </el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="22">
              <el-button type="primary" style="width:100%" @click="login('form')">登录</el-button>
          </el-col>
        </el-row>
        
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
      msg: 'Welcome to My Bigdata System',
      rules:{
        username:[
          {required:true,message:'用户名不能为空',trigger:'blur'},
          {min:3,max:10,message:'用户名3-10位',trigger:'blur'}
        ],
        password:[
          {required:true,message:'密码不能为空',trigger:'blur'},
          {min:3,max:10,message:'密码3-10位',trigger:'blur'}
        ]
      }
    }
  },
  mounted(){
    localStorage.setItem('loginname',undefined)
  },
  methods:{
    register(){

    },
    login(form){
      this.$refs[form].validate(valid=>{
        if(valid){
          this.$axios.get('/tovalid?username='+this.form.username+'&password='+this.form.password)
          .then(result => {
            console.log("result:"+JSON.stringify(result.data,null,2))
            let message = result.data.message
            localStorage.setItem('loginname',result.data.user.username)
            if(message == ''){
              this.$router.push({path:'/HelloWorld'})
            }
          })
          .catch(exp => {
            console.log(exp)
          })
        }else {
          return false
        }
      })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.el-form-item{
  margin-bottom: 20px;
}
.el-row{
  margin-left:30px;
}
/* 背景 */
.login-container {
  position: absolute;
  width: 100%;
  height: 100%;
  /* background: url("../assets/loginbackground.jpg"); */
  background-repeat: no-repeat;
}

.login-form {
  width: 350px;
  margin: 160px auto; /* 上下间距160px，左右自动居中*/
  background-color: rgb(255, 255, 255, 0.8); /* 透明背景色 */
  padding: 38px;
  border-radius: 20px; /* 圆角 */
}


/* 标题 */
.login-title {
  color: #303133;
  text-align: center;
}


.animated {
    -webkit-animation-duration: 1s;
    animation-duration: 1s;
    -webkit-animation-fill-mode: both;
    animation-fill-mode: both;
}

.animated.hinge {
    -webkit-animation-duration: 2s;
    animation-duration: 2s;
}

@-webkit-keyframes bounce {
    0%, 20%, 50%, 80%, 100% {
        -webkit-transform: translateY(0);
        transform: translateY(0);
    }

    40% {
        -webkit-transform: translateY(-30px);
        transform: translateY(-30px);
    }

    60% {
        -webkit-transform: translateY(-15px);
        transform: translateY(-15px);
    }
}

@keyframes bounce {
    0%, 20%, 50%, 80%, 100% {
        -webkit-transform: translateY(0);
        -ms-transform: translateY(0);
        transform: translateY(0);
    }

    40% {
        -webkit-transform: translateY(-30px);
        -ms-transform: translateY(-30px);
        transform: translateY(-30px);
    }

    60% {
        -webkit-transform: translateY(-15px);
        -ms-transform: translateY(-15px);
        transform: translateY(-15px);
    }
}

.bounce {
    -webkit-animation-name: bounce;
    animation-name: bounce;
}

@-webkit-keyframes flash {
    0%, 50%, 100% {
        opacity: 1;
    }

    25%, 75% {
        opacity: 0;
    }
}

@keyframes flash {
    0%, 50%, 100% {
        opacity: 1;
    }

    25%, 75% {
        opacity: 0;
    }
}

.flash {
    -webkit-animation-name: flash;
    animation-name: flash;
}

/* originally authored by Nick Pettit - https://github.com/nickpettit/glide */

@-webkit-keyframes pulse {
    0% {
        -webkit-transform: scale(1);
        transform: scale(1);
    }

    50% {
        -webkit-transform: scale(1.1);
        transform: scale(1.1);
    }

    100% {
        -webkit-transform: scale(1);
        transform: scale(1);
    }
}

@keyframes pulse {
    0% {
        -webkit-transform: scale(1);
        -ms-transform: scale(1);
        transform: scale(1);
    }

    50% {
        -webkit-transform: scale(1.1);
        -ms-transform: scale(1.1);
        transform: scale(1.1);
    }

    100% {
        -webkit-transform: scale(1);
        -ms-transform: scale(1);
        transform: scale(1);
    }
}

.pulse {
    -webkit-animation-name: pulse;
    animation-name: pulse;
}

@-webkit-keyframes shake {
    0%, 100% {
        -webkit-transform: translateX(0);
        transform: translateX(0);
    }

    10%, 30%, 50%, 70%, 90% {
        -webkit-transform: translateX(-10px);
        transform: translateX(-10px);
    }

    20%, 40%, 60%, 80% {
        -webkit-transform: translateX(10px);
        transform: translateX(10px);
    }
}

@keyframes shake {
    0%, 100% {
        -webkit-transform: translateX(0);
        -ms-transform: translateX(0);
        transform: translateX(0);
    }

    10%, 30%, 50%, 70%, 90% {
        -webkit-transform: translateX(-10px);
        -ms-transform: translateX(-10px);
        transform: translateX(-10px);
    }

    20%, 40%, 60%, 80% {
        -webkit-transform: translateX(10px);
        -ms-transform: translateX(10px);
        transform: translateX(10px);
    }
}

.shake {
    -webkit-animation-name: shake;
    animation-name: shake;
}

@-webkit-keyframes swing {
    20% {
        -webkit-transform: rotate(15deg);
        transform: rotate(15deg);
    }

    40% {
        -webkit-transform: rotate(-10deg);
        transform: rotate(-10deg);
    }

    60% {
        -webkit-transform: rotate(5deg);
        transform: rotate(5deg);
    }

    80% {
        -webkit-transform: rotate(-5deg);
        transform: rotate(-5deg);
    }

    100% {
        -webkit-transform: rotate(0deg);
        transform: rotate(0deg);
    }
}

@keyframes swing {
    20% {
        -webkit-transform: rotate(15deg);
        -ms-transform: rotate(15deg);
        transform: rotate(15deg);
    }

    40% {
        -webkit-transform: rotate(-10deg);
        -ms-transform: rotate(-10deg);
        transform: rotate(-10deg);
    }

    60% {
        -webkit-transform: rotate(5deg);
        -ms-transform: rotate(5deg);
        transform: rotate(5deg);
    }

    80% {
        -webkit-transform: rotate(-5deg);
        -ms-transform: rotate(-5deg);
        transform: rotate(-5deg);
    }

    100% {
        -webkit-transform: rotate(0deg);
        -ms-transform: rotate(0deg);
        transform: rotate(0deg);
    }
}

.swing {
    -webkit-transform-origin: top center;
    -ms-transform-origin: top center;
    transform-origin: top center;
    -webkit-animation-name: swing;
    animation-name: swing;
}

@-webkit-keyframes tada {
    0% {
        -webkit-transform: scale(1);
        transform: scale(1);
    }

    10%, 20% {
        -webkit-transform: scale(0.9) rotate(-3deg);
        transform: scale(0.9) rotate(-3deg);
    }

    30%, 50%, 70%, 90% {
        -webkit-transform: scale(1.1) rotate(3deg);
        transform: scale(1.1) rotate(3deg);
    }

    40%, 60%, 80% {
        -webkit-transform: scale(1.1) rotate(-3deg);
        transform: scale(1.1) rotate(-3deg);
    }

    100% {
        -webkit-transform: scale(1) rotate(0);
        transform: scale(1) rotate(0);
    }
}

@keyframes tada {
    0% {
        -webkit-transform: scale(1);
        -ms-transform: scale(1);
        transform: scale(1);
    }

    10%, 20% {
        -webkit-transform: scale(0.9) rotate(-3deg);
        -ms-transform: scale(0.9) rotate(-3deg);
        transform: scale(0.9) rotate(-3deg);
    }

    30%, 50%, 70%, 90% {
        -webkit-transform: scale(1.1) rotate(3deg);
        -ms-transform: scale(1.1) rotate(3deg);
        transform: scale(1.1) rotate(3deg);
    }

    40%, 60%, 80% {
        -webkit-transform: scale(1.1) rotate(-3deg);
        -ms-transform: scale(1.1) rotate(-3deg);
        transform: scale(1.1) rotate(-3deg);
    }

    100% {
        -webkit-transform: scale(1) rotate(0);
        -ms-transform: scale(1) rotate(0);
        transform: scale(1) rotate(0);
    }
}

.tada {
    -webkit-animation-name: tada;
    animation-name: tada;
}

/* originally authored by Nick Pettit - https://github.com/nickpettit/glide */

@-webkit-keyframes wobble {
    0% {
        -webkit-transform: translateX(0%);
        transform: translateX(0%);
    }

    15% {
        -webkit-transform: translateX(-25%) rotate(-5deg);
        transform: translateX(-25%) rotate(-5deg);
    }

    30% {
        -webkit-transform: translateX(20%) rotate(3deg);
        transform: translateX(20%) rotate(3deg);
    }

    45% {
        -webkit-transform: translateX(-15%) rotate(-3deg);
        transform: translateX(-15%) rotate(-3deg);
    }

    60% {
        -webkit-transform: translateX(10%) rotate(2deg);
        transform: translateX(10%) rotate(2deg);
    }

    75% {
        -webkit-transform: translateX(-5%) rotate(-1deg);
        transform: translateX(-5%) rotate(-1deg);
    }

    100% {
        -webkit-transform: translateX(0%);
        transform: translateX(0%);
    }
}

</style>
