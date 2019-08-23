<template>
  <div class="hello">
    <el-button @click="dialogVisible = true">新增</el-button>
    <el-table
      border
      highlight-current-row
      :data="data">
      <el-table-column
        label="空间名称"
        prop="bucket">
      </el-table-column>
      <el-table-column
        label="域名"
        prop="bucket">
      </el-table-column>
      <el-table-column label="操作">
        <template>
          <el-button type="primary">管理</el-button>
          <el-button type="danger">删除</el-button>
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
      }
    }
  },
  methods: {
    addBucket () {
      axios.post(`http://ad.huishimed.com:3000/buckets`, this.form)
        .then(res => {
          // eslint-disable-next-line no-console
          console.log(res)
        })
    }
  },
  created () {
    axios.get(`http://ad.huishimed.com:3000/buckets`)
      .then(res => {
        // eslint-disable-next-line no-console
        console.log(res)
        if (res.status === 200) {
          this.data = res.data
        }
      })

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
