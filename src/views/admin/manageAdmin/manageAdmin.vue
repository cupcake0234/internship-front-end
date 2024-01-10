<template>
  <div>
<!--    <el-table :data="admins" class="admin-table" highlight-current-row>-->
    <el-table :data="admins.slice((currentPage-1)*pageSize,currentPage*pageSize)" class="admin-table" highlight-current-row>
<!--      <el-table :data="admins.slice((currentPage-1)*pageSize,currentPage*pageSize)" class="admin-table" style="width: 100%">-->
      <el-table-column type="index" align="center"/>
      <el-table-column prop="realname" label="姓名" align="center"/>
      <el-table-column prop="telephone" label="联系电话" align="center"/>
      <el-table-column align="center" width="50px" v-if="getAdmin.level === '超级管理员'">
        <template slot-scope="scope">
<!--          <el-popconfirm @onConfirm="deleteAdmin(scope.row.id, scope.row.realname, scope.$index)"-->
<!--                         title="确定要删除该管理员吗" icon="el-icon-warning" iconColor="#F56C6C">-->
<!--            <el-button slot="reference" type="danger" icon="el-icon-delete" size="mini" circle-->
<!--                       v-if=" scope.row.level === '普通管理员' "/>-->
<!--          </el-popconfirm>-->
          <el-button v-if=" scope.row.level === '普通管理员' " type="text" @click="deleteAdmin(scope.row.id, scope.row.realname, scope.$index)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <div class="block" style="margin-top:15px;">
      <el-pagination align='center' @size-change="handleSizeChange" @current-change="handleCurrentChange"
                     :current-page="currentPage"
                     :page-sizes="[1,5,10,20]"
                     :page-size="pageSize"
                     layout="total, sizes, prev, pager, next, jumper"
                     :total="admins.length">
      </el-pagination>
    </div>
  </div>
</template>

<script>
  import {admins, deleteAdmin} from "network/admin"
  import {mapGetters} from 'vuex'

  export default {
    name: "manageAdmin",
    data() {
      return {
        admins: [],
        currentPage: 1, // 当前页码
        total: 30, // 总条数
        pageSize: 10 // 每页的数据条数
      }
    },
    computed: {
      ...mapGetters(['getAdmin']),
    },
    created() {
      this.showAdmins()
    },
    methods: {
      // 网络请求
      showAdmins() {
        admins().then(res => {
          if (res === null) {
            this.$message({
              message: '信息获取失败',
              type: 'error',
              showClose: true,
              center: true,
            });
          } else {
            this.admins = res;
          }
        })
      },
      deletetest(id){
        console.log("deletetest中");
      },
      deleteAdmin(id, realname, index) {
        deleteAdmin(id).then(res => {
          if (res !== 1) {
            this.$message({
              message: realname + '管理员删除失败，请重试',
              type: 'error',
              showClose: true,
              center: true,
            });
          } else {
            this.$message({
              message: realname + '管理员删除成功',
              type: 'success',
              showClose: true,
              center: true,
            });
            this.admins.splice(index, 1);
          }
        })
      },
      //每页条数改变时触发 选择一页显示多少行
      handleSizeChange(val) {
        console.log(`每页 ${val} 条`);
        this.currentPage = 1;
        this.pageSize = val;
      //   在table中的admins是全部数据，换页时slice函数会进行处理分页
      //   这里改变页时和改变size时不用重新载入数据
      },
      //当前页改变时触发 跳转其他页
      handleCurrentChange(val) {
        console.log(`当前页: ${val}`);
        this.currentPage = val;
      },
    }
  }
</script>

<style scoped>
  .admin-table {
    //width: calc(100vw - 165px);
    width: calc(100vw - 165px);
    height: calc(90vh - 60px);
    margin-left: 146px;
  }
</style>
