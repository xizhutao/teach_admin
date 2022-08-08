<template>
  <div>
    <el-button type="text" @click="dialogVisible = true">
      <el-button type="primary"><i class="el-icon-edit"></i></el-button>
    </el-button>
    <el-dialog
      title="编辑用户"
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
  </div>
</template>
<script>
export default {
  created () { },
  data () {
    return {
      dialogVisible: false,
      value: 0,
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
    }
  },
  computed: {},
  watch: {},
  filters: {},
  components: {}
}
</script>

<style scoped lang='less'>
.el-input {
  width: 500px;
}
.textarea {
  width: 600px;
}
i {
  font-size: 20px;
}
</style>
