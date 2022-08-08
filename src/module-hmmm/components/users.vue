<template>
  <div>
    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <span
          ><el-input v-model="input" placeholder="根据用户名搜索"></el-input
        ></span>
        <el-button>清空</el-button>
        <el-button type="primary">搜索</el-button>
        <el-button
          style="float: right; padding: 3px 0"
          type="success"
          @click="addUser"
          ><i class="el-icon-edit"></i><span> 新增用户</span></el-button
        >
      </div>
      <el-dialog
        title="创建用户"
        :visible.sync="dialogVisible"
        width="50%"
        :before-close="handleClose"
      >
        <el-form
          label-width="100px"
          :rules="usersRules"
          :model="usersForm"
          ref="usersForm"
        >
          <el-form-item label="用户名" prop="userName">
            <el-input v-model="usersForm.userName"></el-input>
          </el-form-item>
          <el-form-item label="邮箱" prop="email">
            <el-input v-model="usersForm.email"></el-input>
          </el-form-item>
          <el-form-item label="密码" prop="password">
            <el-input v-model="usersForm.password"></el-input>
          </el-form-item>
          <el-form-item label="角色">
            <el-input v-model="usersForm.role"></el-input>
          </el-form-item>
          <el-form-item label="权限组名称">
            <el-select v-model="value" placeholder="请选择">
              <el-option
                v-for="item in options"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              >
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="联系电话" prop="mobile">
            <el-input v-model="usersForm.mobile"></el-input>
          </el-form-item>
          <el-form-item label="文本" class="textarea">
            <el-input
              type="textarea"
              :rows="2"
              placeholder="请输入内容"
              v-model="textarea"
            ></el-input>
          </el-form-item>
        </el-form>
        <span slot="footer" class="dialog-footer">
          <el-button @click="dialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="dialogVisible = false"
            >确 定</el-button
          >
        </span>
      </el-dialog>
      <div>
        <el-alert
          title="共1条信息"
          type="info"
          :closable="false"
          show-icon
        ></el-alert>
      </div>
      <el-table :data="getUserList" style="width: 100%">
        <el-table-column prop="id" label="序号"> </el-table-column>
        <el-table-column prop="email" label="邮箱"> </el-table-column>
        <el-table-column prop="phone" label="联系电话"> </el-table-column>
        <el-table-column prop="username" label="用户名"> </el-table-column>
        <el-table-column prop="power" label="权限组名称"> </el-table-column>
        <el-table-column prop="role" label="角色"> </el-table-column>
        <el-table-column prop="operate" label="操作">
          <Dialog></Dialog>
        </el-table-column>
      </el-table>
      <div class="block">
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :page-sizes="[1, 5, 10, 20]"
          :page-size="pagesize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total"
        >
        </el-pagination>
      </div>
    </el-card>
  </div>
</template>

<script>
import { list } from '@/api/base/users'
import Dialog from './component/dialog.vue'
export default {
  created () { this.list() },
  data () {
    return {
      input: '',
      value: 0,
      dialogVisible: false,
      pagenum: 1,
      pagesize: 5,
      total: 0,
      getUserList: [],
      textarea: '',
      usersForm: {
        userName: '',
        password: '',
        email: '',
        mobile: '',
        role: ''
      },
      options: [{}],
      usersRules: {
        userName: [
          { required: true, message: '请输入用户名称', trigger: 'blur' },
          { min: 2, max: 7, message: '用户名长度2-7之间', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          { min: 2, max: 7, message: '密码长度2-7之间', trigger: 'blur' }
        ],
        email: [
          { required: true, message: '请输入用户邮箱', trigger: 'blur' },
          {
            pattern:
              /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/,
            message: '邮箱格式不正确',
            trigger: 'blur'
          }
        ],
        mobile: [
          { required: true, message: '请输入用户手机号', trigger: 'blur' },
          {
            pattern: /^(?:(?:\+|00)86)?1\d{10}$/,
            message: '手机号格式不正确',
            trigger: 'blur'
          }
        ]
      }

    }
  },
  methods: {
    handleClose (done) {
      this.$confirm('确认关闭？')
        .then(_ => {
          done()
        })
        .catch(_ => { })
    },
    handleSizeChange (val) {
      this.pagesize = val
      this.list()
    },
    handleCurrentChange (val) {
      this.page = val
      this.list()
    },
    async list () {
      try {
        const { data } = await list({
          page: this.page,
          pagesize: this.pagesize
        })
        console.log(data)
        this.getUserList = data.list
        this.total = data.counts
      } catch (err) {
        console.log(err)
      }
    },
    addUser () {
      this.dialogVisible = true
    }
  },
  computed: {},
  watch: {},
  filters: {},
  components: { Dialog }
}
</script>

<style scoped lang='less'>
.el-card {
  margin: 50px;
  .el-input {
    width: 300px;
  }
}
.el-table {
  margin-top: 20px;
}
i {
  font-size: 25px;
}
span {
  font-size: 20px;
}
.el-form-item {
  .el-input {
    width: 500px;
  }
}
.textarea {
  width: 600px;
}
</style>
