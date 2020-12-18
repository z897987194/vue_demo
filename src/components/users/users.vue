<template>
  <div>
    <!--  面包屑-->
    <el-card class="box-card">
      <el-breadcrumb separator="/">
        <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
        <el-breadcrumb-item>用户管理</el-breadcrumb-item>
        <el-breadcrumb-item>用户列表</el-breadcrumb-item>
      </el-breadcrumb>

      <!--    搜索-->
      <el-row>
        <el-col>
          <div style="margin-top: 15px;">
            <el-input placeholder="请输入内容" @clear="reloadUser" v-model="query" clearable class="inputSearch">
              <el-button @click="searchUser" slot="append" icon="el-icon-search"></el-button>
            </el-input>
            <!-- 添加用户 -->
            <el-button type="success" @click="dialogFormVisibleAdd = true">添加用户</el-button>
          </div>
        </el-col>
      </el-row>

      <!--    表格-->
      <template>
        <el-table
          :data="tableData"
          style="width: 100%">
          <!--序号-->
          <el-table-column
            prop="num"
            label="#"
            type="index"
            width="50">
          </el-table-column>
          <el-table-column
            prop="username"
            label="用户名"
            width="150">
          </el-table-column>
          <el-table-column
            prop="email"
            label="邮箱"
            width="240">
          </el-table-column>
          <el-table-column
            prop="mobile"
            label="电话"
            width="150">
          </el-table-column>

          <el-table-column
            prop="create_time"
            label="创建日期"
            width="120">
            <template slot-scope="tableData">
              {{tableData.row.create_time | fmtdate}}
            </template>
          </el-table-column>

          <el-table-column
            prop="mg_state"
            label="用户状态"
            width="80">
            <template slot-scope="scope">
              <el-switch
                @change="changeMgState(scope.row)"
                style="display: block"
                v-model="scope.row.mg_state"
                active-color="#13ce66"
                inactive-color="#ff4949">
              </el-switch>
            </template>

          </el-table-column>
          <el-table-column
            prop="operation"
            label="操作">
            <template slot-scope="scope">
              <el-row>
                <!-- 编辑按钮 -->
                <el-button @click="editUserBtn(scope.row)" type="primary" icon="el-icon-edit" plain size="small" circle></el-button>
                <!-- 删除按钮 -->
                <el-button @click="deleteBox(scope.row.id)" type="danger" icon="el-icon-delete" plain size="small" circle></el-button>
                <!-- 改变用户角色按钮 -->
                <el-button @click="changeRoleBtn(scope.row)" type="success" icon="el-icon-check" plain size="small" circle></el-button>
              </el-row>
            </template>
          </el-table-column>
        </el-table>
      </template>

      <!-- 对话框 -->
      <!-- 添加用户的对话框 -->
      <el-dialog title="添加用户" :visible.sync="dialogFormVisibleAdd">
        <el-form :model="addUserForm">
          <el-form-item label="用户名" :label-width="formLabelWidth">
            <el-input v-model="addUserForm.username" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="密码" :label-width="formLabelWidth">
            <el-input v-model="addUserForm.password" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="邮箱" :label-width="formLabelWidth">
            <el-input v-model="addUserForm.email" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="电话" :label-width="formLabelWidth">
            <el-input v-model="addUserForm.mobile" autocomplete="off"></el-input>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="dialogFormVisibleAdd = false">取 消</el-button>
          <el-button type="primary" @click="addUser">确 定</el-button>
        </div>
      </el-dialog>
      <!-- 编辑用户对话框 -->
      <el-dialog title="编辑用户" :visible.sync="dialogFormVisibleEdit">
        <el-form :model="editUserForm">
          <el-form-item label="用户名" disabled :label-width="formLabelWidth">
            <el-input v-model="editUserForm.username" disabled autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="邮箱" :label-width="formLabelWidth">
            <el-input v-model="editUserForm.email" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="手机号" :label-width="formLabelWidth">
            <el-input v-model="editUserForm.mobile" autocomplete="off"></el-input>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="dialogFormVisibleEdit = false">取 消</el-button>
          <el-button type="primary" @click="editUser()">确 定</el-button>
        </div>
      </el-dialog>
      <!-- 分配角色对话框 -->
      <el-dialog title="分配角色" width="40%" :visible.sync="dialogFormVisibleRole">
        <el-form :model="roleForm">
          <el-form-item label="用户名" label-width="100px" size="mini">
            {{roleForm.username}}<br>
          </el-form-item>
          <el-form-item label="角色" label-width="100px">
            {{currentRoleId}}
            <el-select v-model="currentRoleId" placeholder="请选择角色">
              <el-option label="请选择" :value="-1" disabled></el-option>
              <el-option v-for="(item, i) in roleList" :label="item.roleName" :value="item.id" :key='i'></el-option>
            </el-select>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="dialogFormVisibleRole = false">取 消</el-button>
          <el-button type="primary" @click="setRole()">确 定</el-button>
        </div>
      </el-dialog>
    </el-card>
    <!-- 分页 -->
    <div class="block">
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="pagenum"
        :page-sizes="[2, 4, 6]"
        :page-size="pagesize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="total">
      </el-pagination>
    </div>
  </div>

</template>

<script>
export default {
  name: "users",
  data() {
    return {
      query: '',
      pagenum: 1,
      pagesize: 2,
      total: -1,
      tableData: [],

      addUserForm: {
        username: '',
        password: '',
        email: '',
        mobile: ''
      },

      editUserForm: {},

      roleForm: {},
      roleList: [],
      currentRoleId: '',
      dialogFormVisibleRole: false,
      dialogFormVisibleEdit: false,
      dialogFormVisibleAdd: false,
      formLabelWidth: '60px'
    }
  },
  created() {
    this.getUserList()
  },
  methods: {
    // 加载用户数据
    async getUserList() {
      // 需要授权的API，必须在请求头中使用Authorization 字段提供token令牌
      const AUTH_TOKEN = localStorage.getItem('token')
      this.$http.defaults.headers.common['Authorization'] = AUTH_TOKEN

      //获取用户列表
      // query  查询参数  可以为空
      // pagenum  当前页码  不能为空
      // pagesize 每页显示数据条数  不能为空
      const res = await this.$http.get('users', {params: {
        "query": this.query, "pagenum": this.pagenum, "pagesize": this.pagesize
        }})
      console.log(res)
      const {meta:{status, msg}, data:{users, total}} = res.data
      if (status === 200) {
        this.tableData = users
        this.total = total
        // this.$message.success(msg)
      } else {
        this.$message.warning(msg)
      }
      // console.log(this.tableData)
    },
    // 改变每页显示数据条数
    handleSizeChange(val) {
      // console.log('每页' + val + '条数据')
      this.pagesize = val
      this.pagenum = 1
      this.getUserList()
    },
    // 改变页数
    handleCurrentChange(val) {
      // console.log('当前页为：' + val)
      this.pagenum = val
      this.getUserList()
    },
    // 搜索用户
    searchUser() {
      this.getUserList()
    },
    // 点击搜索框的清除键时重新加载用户数据
    reloadUser() {
      this.getUserList()
    },
    // 添加用户
    async addUser() {
      const res = await this.$http.post('users', this.addUserForm)
      console.log(res)
      const {meta:{status, msg}, data} = res.data
      if (status === 201) {
        this.$message.success(msg)
        this.dialogFormVisibleAdd = false
        this.getUserList()
        // 清空表单, 也可以简单地使用：this.addUserForm = {}
        for (const key in this.addUserForm) {
          // hasOwnProperty(key)的作用是判断key是否是自己的属性
          if (this.addUserForm.hasOwnProperty(key)) {
            this.addUserForm[key] = ""
          }
        }
      } else {
        this.$message.error(msg)
      }
    },
    // 删除用户
    deleteBox(userId) {
      this.$confirm('此操作将永久删除该用户, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(async () => {
        // 发送删除请求
        const res = await this.$http.delete('users/'+userId)
        // console.log(res)
        if (res.data.meta.status === 200) {
          this.pagenum = 1
          this.getUserList()
          this.$message({
            type: 'success',
            message: '删除成功!'
          });
        }
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });
      });
    },
    // 编辑用户
    editUserBtn(user) {
      this.dialogFormVisibleEdit = true
      this.editUserForm = user
    },
    async editUser() {
      const res = await this.$http.put('users/'+this.editUserForm.id, this.editUserForm)
      // console.log(res)
      const {meta:{status, msg}} = res.data
      if (status === 200) {
        this.dialogFormVisibleEdit = false
        this.$message.success(msg)
        this.getUserList()
      } else {
        this.$message.warning(msg)
      }

    },
    // 修改用户状态
    async changeMgState(user) {
      const res = await this.$http.put('users/'+user.id+'/state/'+user.mg_state)
      // console.log(res)
    },
    //修改用户角色按钮
    async changeRoleBtn(user) {
      this.roleForm = user
      //获取所有角色
      const res1 = await this.$http.get('roles')
      this.roleList = res1.data.data
      // console.log(this.roleList)

      //获取当前用户的角色
      const res2 = await this.$http.get('users/' + user.id)
      // console.log(res2)
      this.currentRoleId = res2.data.data.rid

      this.dialogFormVisibleRole = true
    },
    async setRole() {
      console.log(this.roleForm)
      const res = await this.$http.put('users/'+this.roleForm.id+'/role', {rid: this.currentRoleId})
      // console.log(res)

      this.dialogFormVisibleRole = false
    }

  }
}
</script>

<style scoped>
.box-card {

}

.inputSearch {
  width: 500px;
}
</style>
