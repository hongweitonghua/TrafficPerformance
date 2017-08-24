<template>
    <section>
        <el-row>
            <el-col :span = 8><div class="grid-content"></div></el-col>
            <el-col :span = 8>
                <div class="grid-content">
                    <el-form ref="form" :model="form" label-width="100px">
                        <el-form-item label="原密码" 
                        prop="password"
                        :rules="[{ required: true, message: '请输入原密码', trigger: 'blur' },
                                { min: 6, max: 16, message: '长度在 6 到 16 个字符', trigger: 'blur' }]">
                            <el-input type="password" v-model="form.password" :maxlength = "16" auto-complete="off"></el-input>
                        </el-form-item>
                        <el-form-item label="新密码"
                        prop="passwordN"
                        :rules="[{ required: true, message: '请新输入密码', trigger: 'blur' },
                        { min: 6, max: 16, message: '长度在 6 到 16 个字符', trigger: 'blur' }]">
                            <el-input  type="password" v-model="form.passwordN" :maxlength = "16" auto-complete="off"></el-input>
                        </el-form-item>
                        <el-form-item label="重复新密码"
                        prop="passwordR"
                        :rules="[{ required: true, message: '重新输入新密码', trigger: 'blur' },
                        { min: 6, max: 16, message: '长度在 6 到 16 个字符', trigger: 'blur' }]">
                            <el-input type="password" v-model="form.passwordR" :maxlength = "16" auto-complete="off"></el-input>
                        </el-form-item>
                        <el-form-item>
                            <el-button @click="resetForm('form')">重置</el-button>                            
                            <el-button id="btn" type="primary" @click="onSubmit" >修改密码</el-button>
                        </el-form-item>
                    </el-form>
                </div>
            </el-col>
            <el-col :span = 8><div class="grid-content"></div></el-col>
        </el-row>
    </section> 
</template>

<script>
export default {
    data() {
        return {
            btn:{
                load:false
            },
            form:{
                password:'',
                passwordN:'',
                passwordR:''
            }
        }
    },
    methods:{
        onSubmit(){
            var b = true;
            if(this.form.password==""||this.form.password.length<6)
                b = false;
            if(this.form.passwordN==""||this.form.passwordN.length<6)
                b = false;
            if(this.form.passwordR==""||this.form.passwordR.length<6)
                b = false;
            if(!b) {
                this.$message.error('请输入正确格式！');
            }else if(this.form.passwordR != this.form.passwordN ){
                this.$message.error('两次输入密码不同！');    
            }else{
                var _this = this;
                
                $.get(this.Constant.ajaxAddress+this.Constant.setPasswordAjax,
                    {   username:_this.$userInfo.username,
                        password:$.md5(_this.form.password),
                        passwordN:$.md5(_this.form.passwordN),
                        token:_this.$token
                    })
                    .done(function(data){
                        console.log(data);
                        if(data.errCode==20){//成功
                            _this.$message('修改成功！');
                            _this.form.password = '';
                            _this.form.passwordN = '';
                            _this.form.passwordR = '';
                        }else if(data.errCode==23){//原密码错误
                            _this.$message.error('修改失败，原密码错误！'); 
                        }else if(data.errCode==21){//未知错误
                            _this.$message.error('修改失败，未知错误！'); 
                        }else if(data.errCode==44){//
                           _this.$router.push('/login');
                        }

                    });
            }
        },
        resetForm(formName) {
            this.$refs[formName].resetFields();
        }
    }

    


 }
</script>

<style scoped lang="scss">
    .grid-content{
        border-radius: 8px;
        min-height: 300px;
    }

    .bg-purple{
        background-color: #F2F2F2; 
    }
    .el-row{
        margin-top:100px;
    }
</style>