<!--登录模块-->
<template>
  <div class="login-warp">
    <el-form class="login-form" label-position="top" label-width="80px" :model="formLogin">
      <h2>用户登录</h2>
      <el-form-item label="用户名">
        <el-input v-model="formLogin.username"></el-input>
      </el-form-item>
      <el-form-item label="密码">
        <el-input placeholder="请输入密码" show-password v-model="formLogin.password"></el-input>
      </el-form-item>
      <el-button @click.prevent="handleLogin()" class="login-btn" type="primary">登录</el-button>
    </el-form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      labelPosition: 'top',
      formLogin: {
        username: '',
        password: '',
      }
    };
  },
  methods: {
    //登录请求
    handleLogin() {
      this.$http.post('login', this.formLogin).then(res => {
        console.log(res)
        //登录成功时将返回结果的data里面寻找data和meta,data直接赋值,再将meta里的msg和status对应赋值
        const {
          data,meta:{msg,status}
        } = res.data

        //登录成功时跳转,保持用户信息与token值
        if (status === 200) {
          localStorage.setItem('token',data.token)
          // localStorage.setItem('')
          this.$router.push({name:'home'})
          this.$message.success(msg)
        }
        //失败时提示错误信息
        else {
          this.$message.error(msg)
        }
      })
    }
  }
}
</script>

<style scoped>
.login-warp{
  height: 100%;
  background-color: #324152;
  display: flex;
  justify-content: center;
  align-items: center;
}
.login-warp .login-form{
  margin-top: -50px;
  width: 400px;
  background-color: white;
  border-radius: 5px;
  padding: 0px 30px 30px;
}
.login-btn{
  margin: 15px 0px;
  width: 100%;
}
</style>
