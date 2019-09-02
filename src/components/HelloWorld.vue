<template>
  <div class="hello">
    <el-button @click="dialogVisible = true">新增</el-button>
    <el-table
      @expand-change="onExpandChange"
      border
      highlight-current-row
      :data="data">
      <el-table-column
        type="expand">
        <template v-slot:default="{ row }">
          <div>域名：<span v-for="(d, i) of row.domain" :key="i">{{ i + ':' + d }}</span></div>
        </template>
      </el-table-column>
      <el-table-column
        label="空间名称"
        prop="bucket">
      </el-table-column>
      <el-table-column
        label="域名"
        prop="bucket">
      </el-table-column>
      <el-table-column label="操作">
        <template v-slot:default="{ row }">
          <el-button type="primary">管理</el-button>
          <el-button type="danger" @click="onDelete(row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-dialog
      title="新增"
      :visible.sync="dialogVisible"
      width="30%">
      <div>
        <el-form ref="form" :model="form" label-width="80px">
          <el-form-item prop="name" label="名称">
            <el-input v-model="form.name"></el-input>
          </el-form-item>
          <el-form-item label="地域">
            <el-select v-model="form.zone" placeholder="请选择">
              <el-option
                v-for="item in options"
                :key="item.value"
                :label="item.label"
                :value="item.value">
              </el-option>
            </el-select>
          </el-form-item>
        </el-form>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="addBucket">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data () {
    return {
      data: [],
      options: [],
      dialogVisible: false,
      form: {
        zone: '',
        name: ''
      },
      domain: []
    }
  },
  methods: {
    onExpandChange (row) {
      axios.get(`http://ad.huishimed.com:3000/domain/${row.bucket}`)
        .then(res => {
          const { data: { code, msg: message, data } } = res
          if (code === 20000) {
            this.$message({
              type: 'message',
              message
            })
            // TODO: 优化显示
            row.domain = data
          } else {
            this.$message({
              type: 'error',
              message: '获取失败'
            });  
          }
        })
        .catch(err => {
          // eslint-disable-next-line no-console
          console.log('err', err)
        })
    },
    getList() {
      axios.get(`http://ad.huishimed.com:3000/bucket`)
        .then(res => {
          // eslint-disable-next-line no-console
          console.log(res)
          if (res.status === 200) {
            this.data = res.data
          }
        })
    },
    addBucket () {
      axios.post(`http://ad.huishimed.com:3000/bucket`, this.form)
        .then(res => {
          // eslint-disable-next-line no-console
          console.log(res)
          this.dialogVisible = false
          this.getList()
        })
    },
    onDelete (row) {
      this.$confirm('此操作将永久删除该存储空间, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.handleDelete(row.bucket)
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });          
      });
    },
    handleDelete (bucketName) {
      axios.delete(`http://ad.huishimed.com:3000/bucket/${bucketName}`)
        .then(res => {
          const { data: { code, msg: message } } = res
          if (code === 20000) {
            this.$message({
              type: 'message',
              message
            })
            this.getList()
          } else {
            this.$message({
              type: 'error',
              message: '删除失败'
            });  
          }
        })
        .catch(err => {
          // eslint-disable-next-line no-console
          console.log('err', err)
        })
    }
  },
  created () {
    this.getList()
    axios.get(`http://ad.huishimed.com:3000/options/zone`)
      .then(res => {
        // eslint-disable-next-line no-console
        console.log(res)
        if (res.status === 200) {
          this.options = res.data
        }
      })
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
