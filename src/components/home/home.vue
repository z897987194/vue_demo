<!--主页面-->
<template>
    <el-container class="container">
      <el-header class="header">
        <el-row>
          <el-col :span="4">
            <div class="grid-content bg-black">
              <img src="../../assets/logo.jpg" alt="无法显示图片">
            </div>
          </el-col>
          <el-col :span="18" class="header-middle">
            <h3>后台管理系统</h3>
          </el-col>
          <el-col :span="2">
            <div class="grid-content bg-purple">
              <a class="logout" @click.prevent="handleLogout()" href="#">注销</a>
            </div>
          </el-col>
        </el-row>
      </el-header>

      <el-container>
<!--        侧边栏导航-->
        <el-aside class="aside" width="200px">
<!--          开启路由-->
          <el-menu :unique-opened="true" :router="true">
            <el-submenu index="1">
              <template slot="title">
                <i class="el-icon-location"></i>
                <span>用户管理</span>
              </template>
              <el-menu-item index="users">
                <i class="el-icon-menu"></i>
                <span>用户列表</span>
              </el-menu-item>
            </el-submenu>

            <el-submenu index="2">
              <template slot="title">
                <i class="el-icon-location"></i>
                <span>权限管理</span>
              </template>
              <el-menu-item index="2-1">
                <i class="el-icon-menu"></i>
                <span>角色列表</span>
              </el-menu-item>
              <el-menu-item index="2-2">
                <i class="el-icon-menu"></i>
                <span>权限列表</span>
              </el-menu-item>
            </el-submenu>

            <el-submenu index="3">
              <template slot="title">
                <i class="el-icon-location"></i>
                <span>商品管理</span>
              </template>
              <el-menu-item index="3-1">
                <i class="el-icon-menu"></i>
                <span>商品列表</span>
              </el-menu-item>
              <el-menu-item index="3-2">
                <i class="el-icon-menu"></i>
                <span>分类参数</span>
              </el-menu-item>
              <el-menu-item index="3-3">
                <i class="el-icon-menu"></i>
                <span>商品分类</span>
              </el-menu-item>
            </el-submenu>

            <el-submenu index="4">
              <template slot="title">
                <i class="el-icon-location"></i>
                <span>订单管理</span>
              </template>
              <el-menu-item index="4-1">
                <i class="el-icon-menu"></i>
                <span>订单列表</span>
              </el-menu-item>
            </el-submenu>

            <el-submenu index="5">
              <template slot="title">
                <i class="el-icon-location"></i>
                <span>数据统计</span>
              </template>
              <el-menu-item index="5-1">
                <i class="el-icon-menu"></i>
                <span>数据报表</span>
              </el-menu-item>
            </el-submenu>

          </el-menu>
        </el-aside>

        <el-main class="main">
          <router-view></router-view>
        </el-main>
      </el-container>


    </el-container>
</template>

<script>
export default {
  //获取token -> 判断是否登录
  beforeCreate() {
    const token = localStorage.getItem('token')
    if (!token) {
      this.$router.push({ name: 'login' })
      this.$message.error('登录失败，请重新登录')
    } else {

    }
  },
  methods: {
    handleLogout(){
      //清除token、用户登录信息
      localStorage.clear()
      this.$message.success('退出登录')
      this.$router.push({ name: 'login' })
    }
  }
}
</script>

<style scoped>
.container {
  height: 100%;
}
.header {
  background-color: #B3C0D1;
  text-align: center;
  color: #333;
}

.aside {
  background-color: #D3DCE6;
  color: #333;
  line-height: 200px;
}

.main {
  background-color: #E9EEF3;
  color: #333;
  /*line-height: 160px;*/
}

.header-middle {

}

.logout {
  line-height: 60px;
  text-decoration: none;
}

img {
  width: 100%;
}

</style>
