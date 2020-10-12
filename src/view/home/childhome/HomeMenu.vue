<template>
    <el-menu
      default-active="2"
      class="el-menu-vertical-demo"
      background-color="#545c64"
      text-color="#fff"
      active-text-color="#ffd04b"
      router>
      <el-submenu :index="item.id +''" v-for="item in menus"
      :key="item.id">
        <template slot="title">
          <i class="el-icon-location"></i>
          <span>{{item.authName}}</span>
        </template>
          <el-menu-item :index="'/' + childitem.path"
           v-for="childitem in item.children"
          :key="childitem.id">
            <template slot="title">
              <i class="el-icon-location"></i>
              <span>{{childitem.authName}}</span>
            </template>
          </el-menu-item>
      </el-submenu>
    </el-menu>
</template>

<script>
export default {
  name:'HomeMenu',
  props:{
    menus:{
      type:Array,
      default(){
        return []
      }
    }
  }
}
</script>

<style scoped>
.el-menu{
  height: 100%;
}
</style>
