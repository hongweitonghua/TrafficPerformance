<template>
  <div class="login-wrapper">
    <el-form :model="ruleForm2" :rules="rules2" ref="ruleForm2" label-position="left" label-width="0px" class="demo-ruleForm login-container">
    <h3 class="title">能源监测平台</h3>
    <el-form-item prop="account">
      <el-input type="text" v-model="ruleForm2.account" auto-complete="off" placeholder="账号"></el-input>
    </el-form-item>
    <el-form-item prop="checkPass">
      <el-input type="password" v-model="ruleForm2.checkPass" auto-complete="off" placeholder="密码"></el-input>
    </el-form-item>
    <el-checkbox v-model="checked" checked class="remember" fill="#6bc5a4">记住密码</el-checkbox>
    <span class="forgetPwd" >
         忘记密码？
    </span>
   <!-- <input type="checkbox" name="rempwd" class="remember" v-model="checked" checked>记住密码 -->
    <el-form-item style="width:100%;">
      <el-button type="primary" style="width:100%;" class="loginBtn" @click.native.prevent="handleSubmit2" :loading="logining">
        <i class="fa fa-check"></i></el-button>
      <!--<el-button @click.native.prevent="handleReset2">重置</el-button>-->
    </el-form-item>
     <!-- <div class="registration">
        还没有账号?
          <span class="signup"  @click="handleRegister">
              点击注册
          </span>
    </div> -->
  </el-form>
 
  </div>
</template>

<script>
  import { requestLogin } from '../api/api';
  import {getCookie,delCookie,setCookie} from '../common/js/Cookie.js';
  import vue from 'vue';
  
  //import NProgress from 'nprogress'
  export default {
    data() {
      return {
        logining: false,
        checked: true,
        ruleForm2: {
          account: 'admin',
          checkPass: '123456'
        },
        rules2: {
          account: [
            { required: true, message: '请输入账号', trigger: 'blur' },
            //{ validator: validaePass }
          ],
          checkPass: [
            { required: true, message: '请输入密码', trigger: 'blur' },
            //{ validator: validaePass2 }
          ]
        },
      }
    },
    methods: {
      handleReset2() {
        this.$refs.ruleForm2.resetFields();
      },
      handleSubmit2(ev) {
        
        var _this = this;
        this.$refs.ruleForm2.validate((valid) => {
          if (valid) {
            this.logining = true;
            //NProgress.start();
            var loginParams = { username: this.ruleForm2.account, password: this.ruleForm2.checkPass };
            $.get(this.Constant.ajaxAddress+"/login.json",
                                              { username:loginParams.username,
                                                password:$.md5(loginParams.password)}).done(function(data){
              _this.logining = false;
              console.log(data);
              if(data.errCode==10){
                  // vue.prototype.$token = data.token;
                  // vue.prototype.$userInfo = data.userInfo;
                  var c = JSON.stringify(data.userInfo);
                  setCookie('token',data.token);
                  setCookie('userInfo',c);
                  console.log(document.cookie);
                  _this.$router.push({ path: '/index' });
                }else{
                  window.alert("用户名或者密码错误");
                  
                }
            })
            
          } else {
            console.log('error submit!!');
            return false;
          }
        });
      },
      handleRegister(){
        this.$router.push({path:'/register'});
      }
    }
  }

</script>

<style lang="scss" scoped>
  .login-wrapper{
    position: fixed;
    top: 0;
    left: 0;
    bottom: 0;
    background-image: url(../assets/login-bg.jpg);
    background-size: cover;
    width: 100%;
    height: 100%;
    .login-container {
      /*box-shadow: 0 0px 8px 0 rgba(0, 0, 0, 0.06), 0 1px 0px 0 rgba(0, 0, 0, 0.02);*/
      position: relative;
      -webkit-border-radius: 5px;
      border-radius: 5px;
      -moz-border-radius: 5px;
      background-clip: padding-box;
      margin: 180px auto;
      width: 300px;
      padding: 35px 20px 15px 20px;
      background: #fff;
      border: 1px solid #eaeaea;
      .title {
        position: absolute;
        top: -60px;
        width: 100%;
        text-align: center;
        left: 0;
        margin: 0px auto 40px auto;
        text-align: center;
        color: #505458;
        font-size: 24px;
        color: #fff;
      }
      .forgetPwd{
        float: right;
        font-size: 12px;
        color: #6bc5a4;
      }
      .forgetPwd:hover{
        text-decoration: underline;
      }
      .remember {
        margin: 0px 0px 20px 0px;    
        font-size: 12px;
      }
      .el-checkbox__inner:hover{
         border-color: #6bc5a4 !important;
      }
      
      .el-checkbox__input.is-checked{
        .el-checkbox__inner:hover{
             border-color: #6bc5a4 !important;
         }
      }
      .loginBtn{
        background: #6bc5a4;
        /*background: #20a0ff;*/
        color: #fff;
        text-transform: uppercase;
        font-weight: normal;
        font-family: 'Open Sans', sans-serif;
           
        padding: 8px;
        -webkit-transition: all 0.3s;
        -moz-transition: all 0.3s;
        transition: all 0.3s;
        font-size: 30px;
        border: none;
      }
      .loginBtn:hover{
        background: #688ac2;
        transition: all 0.3s;
      }
      .registration{
        color: #c7c7c7;
        text-align: center;
        margin-top: 15px;
        .signup{
          color: #6bc5a4;
          text-decoration: none;
          cursor: pointer;
        }
        .signup:hover{
          text-decoration: underline;
        }
      }

    }
  }
</style>