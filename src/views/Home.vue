<template>
  <div style="padding: 10px" >
<!--    功能区-->
    <div style="margin-bottom: 10px">
      <el-button type="primary" @click="add">新增</el-button>
      <el-button type="primary">导入</el-button>
      <el-button type="primary">导出</el-button>
    </div >
<!--    搜索区-->
    <div style="margin-bottom: 10px">
      <el-input v-model="search" placeholder="请输入关键字" style="width: 20%" clearable></el-input>
      <el-button type="primary" style="margin-left: 5px" @click="load" >查询</el-button>
    </div>
    <el-table :data="tableData" border scripe style="width: 99%; min-width: calc(180vh)">
      <el-table-column prop="id" label="ID" sortable> </el-table-column>
      <el-table-column prop="username" label="用户名" > </el-table-column>
      <el-table-column prop="nickName" label="昵称" > </el-table-column>
      <el-table-column prop="age" label="年龄" sortable> </el-table-column>
      <el-table-column prop="sex" label="性别" > </el-table-column>
      <el-table-column prop="address" label="地址" > </el-table-column>
      <el-table-column fixed="right" label="操作" width="200">
        <template #default = "scope">
          <el-button  size="mini" @click="handleEdit(scope.row)">编辑</el-button>
          <el-popconfirm title="确定删除吗？" @confirm="handleDelete(scope.row.id)">
            <template #reference>
              <el-button size="mini" type="danger">删除</el-button>
            </template>
          </el-popconfirm>
        </template>
      </el-table-column>
    </el-table>
    <div style="margin: 10px 0">
      <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :currentPage="currentPage"
          :page-sizes="pageSize"
          :page-size="10"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total"
      />
      <el-dialog v-model="dialogVisible" title="提示" width="30%">
        <el-form :model="form" label-width="120px">
          <el-form-item label="用户名">
            <el-input v-model="form.username" style="width: 80%"/>
          </el-form-item>
          <el-form-item label="昵称">
            <el-input v-model="form.nickName" style="width: 80%"/>
          </el-form-item>
          <el-form-item label="年龄">
            <el-input v-model="form.age" style="width: 80%"/>
          </el-form-item>
          <el-form-item label="性别">
            <el-radio v-model="form.sex" label="男">男</el-radio>
            <el-radio v-model="form.sex" label="女">女</el-radio>
            <el-radio v-model="form.sex" label="未知">未知</el-radio>
          </el-form-item>
          <el-form-item label="地址">
            <el-input type="textarea" v-model="form.address" style="width: 80%"/>
          </el-form-item>

        </el-form>
        <template #footer>
        <span class="dialog-footer">
          <el-button @click="dialogVisible = false">取消</el-button>
          <el-button type="primary" @click="save">确认</el-button>
        </span>
        </template>
      </el-dialog>

    </div>

  </div>
</template>

<script>


import request from "@/utils/request";

export default {
  name: 'Home',
  components: {

  },
  data() {
    return {
      form: {},
      dialogVisible: false,
      search: '',
      currentPage: 1,
      pageSize: 10,
      total: 0,
      tableData: [

      ]
    }
  },
  created() {
    this.load()
  },
  methods: {
    load() {
      request.get("http://localhost:9090/user",{
        params: {
          pageNum: this.currentPage,
          pageSize: this.pageSize,
          search: this.search
        },
        pageNum: this.currentPage,
        pageSize: this.pageSize,
        search: this.search
      }).then(res => {
        this.tableData = res.data.records,
        this.total = res.data.total
      })
    },
    add() {
      this.dialogVisible = true
      this.form = {}  // 清空表单
    },
    save() {
      if (this.form.id) {  // 如果有 id 就是更新
        request.put("http://localhost:9090/user",this.form).then(res => {
          console.log(res)
          if (res.code === '0') {
            this.$message({
              type: "success",
              message: "更新成功"
            })
          } else {
            this.$message({
              type: "error",
              message: res.msg
            })
          }
        })
      } else {  // 新增
        request.post("http://localhost:9090/user",this.form).then(res => {
          console.log(res)
          if (res.code === '0') {
            this.$message({
              type: "success",
              message: "新增成功"
            })
          } else {
            this.$message({
              type: "error",
              message: res.msg
            })
          }

        })

      }
      this.load() // 刷新表格数据
      this.dialogVisible = false
      // this.dialogVisible = false
    },
    handleEdit(row) {
      this.form = JSON.parse(JSON.stringify(row))  // 将对象于原对象隔离开
      this.dialogVisible = true
    },
    handleDelete(id) {
      console.log(id)
      request.delete("http://localhost:9090/user/" + id).then(res => {
        if (res.code === '0') {
          this.$message({
            type: "success",
            message: "删除成功"
          })
        } else {
          this.$message({
            type: "error",
            message: res.msg
          })
        }
        this.load()  // 重新加载表格

      })
    },
    handleSizeChange(pageSize) {  // 改变每页个数
      this.pageSize = pageSize
      this.load()
    },
    handleCurrentChange(pageNum) {  // 改变当前页码
      this.currentPage = pageNum
      this.load()
    },



  }
}
</script>
