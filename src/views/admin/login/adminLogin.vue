<template>
  <el-form :model="admin" status-icon :rules="rules" ref="ruleForm"
           label-width="100px" class="ruleForm">
    <el-form-item label="用户名" prop="username">
      <el-input v-model="admin.username" placeholder="请输入用户名"/>
    </el-form-item>
    <el-form-item label="密码" prop="pw">
      <el-input type="password" v-model="admin.pw" placeholder="请输入密码" autocomplete="off"/>
    </el-form-item>
    <el-form-item label="验证码" prop="verify" style="width: 445px;">
      <el-input style="width: 225px;" v-model="admin.verify" placeholder="请输入验证码（不区分大小写）"/>
      <canvas ref="changeImg" width="120" height="40" title="看不清，换一张" @click="drawPic"
              style="margin-top: -2px;vertical-align: middle;"/>
    </el-form-item>
    <el-form-item>
      <el-button type="primary" plain @click="submitForm('ruleForm')">登录</el-button>
      <el-button @click="resetForm('ruleForm')">重置</el-button>
    </el-form-item>
  </el-form>
</template>

<script>
  import {captchaMixin} from "common/mixin";
  import {getAdmin} from "network/admin"
  import {mapMutations} from 'vuex'


  export default {
    name: "adminLogin",
    mixins: [captchaMixin],
    created(){
      this.isLogined();
    },
    data() {
      const checkUsername = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请输入用户名'));
        } else {
          callback();
        }
      };
      const validatePw = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请输入密码'));
        } else {
          callback();
        }
      };
      const checkVerify = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请输入验证码'));
        } else if (value.toUpperCase() !== this.captcha) {
          callback(new Error('验证码输入错误'));
        } else {
          callback();
        }
      };
      return {
        admin: {
          username: '',
          pw: '',
          verify: '',
        },
        rules: {
          username: [
            {validator: checkUsername, trigger: 'blur'}
          ],
          pw: [
            {validator: validatePw, trigger: 'blur'}
          ],
          verify: [
            {validator: checkVerify, trigger: 'blur'}
          ],
        }
      };
    },
    methods: {
      ...mapMutations(['setAdmin']),
      isLogined(){
        if(this.$cookies.isKey('adminID')){
          this.$router.push('/admin/Home')
          this.$message({
            message: '已经登陆了',
            type: 'warning',
            showClose: true,
            center: true,
          });
        }
      },
      // 网络请求
      getAdmin() {
        getAdmin(this.admin).then(res => {
          if (res === '') {
            this.$message({
              message: '用户名或密码错误',
              type: 'error',
              showClose: true,
              center: true,
            });
          } else {
            this.$message({
              message: '登录成功',
              type: 'success',
              showClose: true,
              center: true,
            });
            this.$cookies.set("adminID",res.id);
            this.setAdmin(res);
            this.$router.push('/admin/Home')
          }
        })
      },
      //表单操作
      submitForm(formName) {
        this.$refs[formName].validate((valid) => {
          if (valid) {
            this.getAdmin();
          } else {
            console.log('error submit!!');
            return false;
          }
        });
      },
      resetForm(formName) {
        this.$refs[formName].resetFields();
      }
    }
  }
</script>

<style scoped>
  .ruleForm {
    width: 400px;
    margin: 50px auto 0;
  }
</style>
