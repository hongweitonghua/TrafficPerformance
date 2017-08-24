<template>
	<div class="register-wrapper">
		<el-form :inline= "true":model="ruleForm" ref = "ruleForm" label-width="110px" class="demo-form-inline demo-ruleForm register-container">
    <!-- <h3 class="title">单位信息</h3> -->
			<p class="text-header">单位信息</p>
      <el-form-item label="组织机构代码" prop="code">
        <el-input v-model="ruleForm.code" placeholder = '不包含"-"号的9位代码' class = "input-in"></el-input>
      </el-form-item>
		  <el-form-item label="单位名称" prop="name">
		    <el-input v-model="ruleForm.name" class = "input-in"></el-input>
		  </el-form-item>
   
		  <el-form-item label="所在地区" prop="region">
		    <el-select v-model="ruleForm.region" placeholder="请选择一个市区" class = "input-in">
		      <el-option label="杭州市" value="shanghai"></el-option>
		      <el-option label="衢州市" value="beijing"></el-option>
		    </el-select>
		  </el-form-item>
      <el-form-item label="所处行业" prop="industry">
        <el-input v-model="ruleForm.industry" class = "input-in"></el-input>
      </el-form-item>
      <el-form-item label="单位类型" prop="type">
        <el-select v-model="ruleForm.type" placeholder="选择单位的类型" class = "input-in">
          <el-option label="类型1" value="shanghai"></el-option>
          <el-option label="类型2" value="beijing"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="注册日期" prop="RegisDate">
        <el-date-picker v-model="ruleForm.RegisDate" type="date" class = "input-in"></el-date-picker>
      </el-form-item>
     
      <el-form-item label="注册金额" prop="RegisAccount">
        <el-input v-model="ruleForm.RegisAccount" class = "input-in"></el-input>
      </el-form-item>
      <el-form-item label="法人代表" prop="Boss">
        <el-input v-model="ruleForm.Boss" class = "input-in"></el-input>
      </el-form-item>
      <el-form-item label="联系电话" prop="tel">
        <el-input v-model="ruleForm.tel" class = "input-in"></el-input>
      </el-form-item>
      <el-form-item label="注册地址" prop="RegisAddress">
        <el-input v-model="ruleForm.RegisAddress" class = "input-in"></el-input>
      </el-form-item>
      <el-form-item label="注册地邮编" prop="RegisPostCode">
        <el-input v-model="ruleForm.RegisPostCode" class = "input-in"></el-input>
      </el-form-item>
      <el-form-item label="经营地址" prop="SellAddress">
        <el-input v-model="ruleForm.SellAddress" class = "input-in"></el-input>
      </el-form-item>
      <el-form-item label="经营地邮编" prop="SellPostCode">
        <el-input v-model="ruleForm.SellPostCode" class = "input-in"></el-input>
      </el-form-item>
       <el-form-item label="经营地电话" prop="SellTel">
        <el-input v-model="ruleForm.SellTel" class = "input-in"></el-input>
      </el-form-item>
      <el-form-item label="官方网址" prop="website">
        <el-input v-model="ruleForm.website" class = "input-in"></el-input>
      </el-form-item>
      <el-form-item label="传真号码" prop="Fax">
        <el-input v-model="ruleForm.Fax" class = "input-in"></el-input>
      </el-form-item>
      <el-form-item label="经营范围" prop="SellRange">
        <el-input v-model="ruleForm.website" class = "input-in"></el-input>
      </el-form-item>
      <div class="registration">
        <el-form-item>
          <el-button type="primary" @click="submitForm('ruleForm')" class="registerBtn">下一步</el-button>
          <el-button @click="resetForm('ruleForm')">重置</el-button>
        </el-form-item>
      </div>
		  
		  
		  <div class="registration">
	        	已有账号?
	          <span class="signup"  @click="backToLogin">
	              点击登录
	          </span>
    	</div>
		</el-form>
		
	</div>
</template>
<script>
	export default {
    data() {
      return {
        ruleForm: {
          code:'',
          name: '',
          region: '',
          industry:'',
          RegisDate:'',
          RegisAccount:'',
          Boss:'',
          tel:'',
          RegisAddress:'',
          RegisPostCode:'',
          SellAddress:'', 
          SellPostCode:'', 
          SellTel:'', 
          website:'', 
          Fax:'', 
          SellRange:'', 
          date1: '',
          date2: '',
          delivery: false,
          type: [],
          resource: '',
          desc: ''
        },
        rules: {
          name: [
            { required: true, message: '请输入活动名称', trigger: 'blur' }
            // { min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' }
          ],
          region: [
            { required: true, message: '请选择活动区域', trigger: 'change' }
          ],
          date1: [
            { type: 'date', required: true, message: '请选择日期', trigger: 'change' }
          ],
          date2: [
            { type: 'date', required: true, message: '请选择时间', trigger: 'change' }
          ],
          type: [
            { type: 'array', required: true, message: '请至少选择一个活动性质', trigger: 'change' }
          ],
          resource: [
            { required: true, message: '请选择活动资源', trigger: 'change' }
          ],
          desc: [
            { required: true, message: '请填写活动形式', trigger: 'blur' }
          ]
        }
      };
    },
    methods: {
      submitForm(formName) {
        this.$refs[formName].validate((valid) => {
          if (valid) {
            // this.$message({
            //       message: '注册成功',
            //       type: 'success'
            // }); 
            //将用户信息添加到数据库
            
            //跳转到登录页
            // alert("123")
             this.$router.push({path:'/scale'});
            // this.$router.push('/scale');

          } else {
            console.log('error submit!!');
            return false;
          }
        });
      },
      resetForm(formName) {
        this.$refs[formName].resetFields();
      },
      backToLogin(){
      	this.$router.go(-1);
      }
    }
  }
</script>
<style scoped lang="scss">
.register-wrapper{
    position: fixed;
    top: 0;
    left: 0;
    bottom: 0;
    background-image: url(../assets/login-bg.jpg);
    background-size: cover;
    width: 100%;
    height: 100%;

    .register-container {
      position: relative;
      -webkit-border-radius: 5px;
      border-radius: 5px;
      -moz-border-radius: 5px;
      background-clip: padding-box;
      margin: 20px auto;
      width: 630px;
      padding: 40px 20px 15px 0;
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
      .text-header{
      	position: absolute;
        top: 0;
        width: 100%;
        margin-left: 15px;

      }
      .registerBtn{
        background: #6bc5a4;
        color: #fff;
        -webkit-transition: all 0.3s;
        -moz-transition: all 0.3s;
        transition: all 0.3s;
        /*font-size: 30px;*/
        border: none;
      }
      .registerBtn:hover{
        background: #688ac2;
        /*background: #20A0FF;*/

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
	    .el-radio__input.is-checked  {
	    	.el-radio__inner{
	    		border-color: #6bc5a4;
		   	 	background: #6bc5a4;
	    	}
		   
		}
	    .el-button:focus, .el-button:hover {
		    color: #6bc5a4;
		    border-color: #6bc5a4;
		}
    .input-in{
      width:190px;
    }

     }

    }
</style>