<template>
  <div id="users">
    <!-- 导航面包屑区域 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片内容区域 -->
    <el-card class="box-card">
      <el-row :gutter="20">
        <el-col :span="7">
          <el-input placeholder="请输入内容"
           class="input-with-select"
           v-model="queryInfo.query">
            <el-button slot="append" icon="el-icon-search" @click="getUsersList"></el-button>
          </el-input>
        </el-col>
        <el-col :span="4">
          <el-button type="primary" @click="change" >添加用户</el-button>
        </el-col>
      </el-row>
      <el-table :data="usersList" border>
        <el-table-column type="index" label="#"></el-table-column>
        <el-table-column label="姓名" prop="username"></el-table-column>
        <el-table-column label="电话" prop="mobile"></el-table-column>
        <el-table-column label="邮箱" prop="email"></el-table-column>
        <el-table-column label="角色" prop="role_name"></el-table-column>
        <el-table-column label="状态">
          <template slot-scope="scope">
            <el-switch v-model="scope.row.mg_state" @change="userStateChage(scope.row)"></el-switch>
          </template>
        </el-table-column>
        <el-table-column label="操作" prop="username" width="200px">
            <template slot-scope="scope">
              <el-tooltip class="item" effect="dark" content="修改用户" placement="top" :enterable="false">
                <el-button type="primary" @click="updateChange(scope.row.id)" icon="el-icon-edit" size="mini"></el-button>
              </el-tooltip>
              <el-tooltip class="item" effect="dark" content="删除用户" placement="top" :enterable="false">
                <el-button type="danger" icon="el-icon-delete" size="mini"></el-button>
              </el-tooltip>
              <el-tooltip class="item" effect="dark" content="分配角色" placement="top" :enterable="false">
                <el-button type="warning" icon="el-icon-setting" size="mini"></el-button>
              </el-tooltip>
            </template>
        </el-table-column>
      </el-table>
      <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="queryInfo.pagenum"
      :page-sizes="[1, 2, 5, 10]"
      :page-size="queryInfo.pagesize"
      layout="total, sizes, prev, pager, next, jumper"
      :total="total">
    </el-pagination>
    <add-users :caddUsersShow="addUsersShow"  @onchange="onchangeshow"></add-users>
    <update-users :cupdateUsers=updateUsers
    @onupdatechange="onupdatechanges"
    :cupdateForm=updateForm
    ></update-users>
    </el-card>
  </div>
</template>

<script>
import AddUsers from './childusers/AddUsers'
import updateUsers from './childusers/UpdateUsers'
export default {
  name:'Users',
  components:{
    AddUsers,
    updateUsers
  },
  data(){
    return{
      queryInfo:{
        query:'',
        pagenum:1,
        pagesize:10
      },
      usersList:[],
      total:0,
      addUsersShow:false,
      updateUsers:false,
      updateForm:{}
    }
  },
  created(){
    this.getUsersList()
  },
  methods:{
    //点击添加用户按钮，父组件布尔值改变
    change(){
      this.addUsersShow=true
    },
    async updateChange(id){
      this.updateUsers=true
      const {data:res} = await this.$http.get('users/'+id)
      if(res.meta.status!=200){
        this.$message.error('获取用户信息失败！')
      }
      this.updateForm=res.data
      console.log(this.updateForm)
    },
    //子组件事件和父组件事件val值联通
    onchangeshow(val){
      this.addUsersShow=val
      if(this.addUsersShow === false){
        this.getUsersList()
      }
    },
    onupdatechanges(val){
      this.updateUsers=val
    },
    async getUsersList(){
     const {data:res} = await this.$http.get('users',{params:this.queryInfo})
     if(res.meta.status !==200){
        return this.$message.error("获取用户数据失败")
     }
    this.usersList=res.data.users
    this.total=res.data.total
    },
    handleSizeChange(newSize){
      this.queryInfo.pagesize = newSize
      this.getUsersList()
    },
    handleCurrentChange(newPage){
      this.queryInfo.pagenum = newPage
      this.getUsersList()
    },
    async userStateChage(userInfo){
      const {data:res} = await this.$http.put(`users/${userInfo.id}/state/${userInfo.mg_state}`)
      if(res.meta.status !== 200){
        userInfo.mg_state !=userInfo.mg_state
        return this.$message.error("修改用户状态失败！")
      }
      this.$message.success("修改用户状态成功！")
    }
  }
}
</script>

<style>

</style>
