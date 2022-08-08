<template>
  <div>
    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <span
          ><el-input v-model="input" placeholder="根据用户名搜索"></el-input
        ></span>
        <el-button>清空</el-button>
        <el-button type="primary">搜索</el-button>
        <el-button style="float: right; padding: 3px 0" type="success"
          ><i class="el-icon-edit"></i><span> 新增用户</span></el-button
        >
      </div>
      <div>
        <el-alert
          title="共1条信息"
          type="info"
          :closable="false"
          show-icon
        ></el-alert>
      </div>

    </el-card>
  </div>
</template>

<script>
export default {
  created () { },
  data () {
    return {
      input: ''
    }
  },
  methods: {

  },
  computed: {},
  watch: {},
  filters: {},
  components: {}
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
