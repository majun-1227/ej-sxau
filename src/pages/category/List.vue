<template>
  <div>
    <!-- 按钮 -->
    <div>
      <el-button size="small" type="primary" @click="toAddHandler">添加</el-button>
      <el-button size="small" type="danger">批量删除</el-button>
    </div>
    <!-- /按钮 -->
    <!-- 表格 -->
    <el-table :data="category">
      <el-table-column fixed="left" prop="id" label="编号"></el-table-column>
      <el-table-column prop="name" label="栏目名称"></el-table-column>
      <el-table-column prop="num" label="序号"></el-table-column>
      <el-table-column prop="parentId" label="父栏目"></el-table-column>
      <el-table-column fixed="right" label="操作">
        <template v-slot="slot">
          <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
          <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
        </template>
      </el-table-column>
    </el-table>
    <!-- /表格 -->
    
    <!-- 分页 -->
    <!-- <el-pagination layout="prev, pager, next" :total="50"></el-pagination> -->
    <!-- /分页 -->
   
    <!-- 模态框 -->
    <el-dialog
      :title="title"
      :visible.sync="visible"
      width="60%">
      <!-- 测试：{{form}} -->
      <el-form :model="form" label-width="80px">
        <el-form-item label="编号">
          <el-input v-model="form.id"/>
        </el-form-item>
        <el-form-item label="栏目名称">
          <el-input v-model="form.name"/>
        </el-form-item>
        <el-form-item label="序号">
          <el-input v-model="form.num"/>
        </el-form-item>
        <el-form-item label="父栏目">
          <el-input v-model="form.parentId"/>
        </el-form-item>
      </el-form>


      <span slot="footer" class="dialog-footer">
        <el-button size="small" @click="closeModalHandler">取 消</el-button>
        <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
      </span>
    </el-dialog>
    <!-- /模态框 -->
  </div>
</template>

<script>
import request from '@/utils/request' // 自定义库
import querystring from 'querystring' // 系统库
export default {
  data(){
    return {
      title:"录入栏目信息",
      visible:false,
      category:[],
      form:{
        type:"category"
      }
    }
  },
  created(){
    // 在页面加载出来的时候加载数据
    this.loadData();
  },
  methods:{
    // 提交
    submitHandler(){
      let url = "http://localhost:6677/category/saveOrUpdate";
      // 前端向后台发送请求，完成数据的保存操作
      request({
        url,
        method:"post",
        // 告诉给后台我的请求体中放的是查询字符串
        headers:{
          "Content-Type":"application/x-www-form-urlencoded"
        },
        // 请求体中的数据，将this.form转换为查询字符串发送给后台
        data:querystring.stringify(this.form)
      }).then((response)=>{
        this.closeModalHandler();
        this.loadData();
        this.$message({type:"success",message:response.message})
      })
    },
    // 重载员工数据
    loadData(){
      // this -> vue实例，通过vue实例访问该实例中数据，methods中其他方法，data中返回值
      // this.title / this.toAddHandler
      let url = "http://localhost:6677/category/findAll";
      request.get(url).then((response)=>{
        // 箭头函数中的this指向外部函数中的this
        this.category = response.data;     
      })
    },
    toAddHandler(){
      this.title = "录入栏目信息";
      this.visible = true;
    },
    toDeleteHandler(id){
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$message({
          type: 'success',
          message: '删除成功!'
        });
      })
    },
    toUpdateHandler(row){
      this.title = "修改栏目信息";
      this.visible = true;
    },
    closeModalHandler(){
      this.visible = false;
    }
  }
}
</script>

<style scoped>
</style>