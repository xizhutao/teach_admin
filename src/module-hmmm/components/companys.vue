<template>
  <el-card class="box-card">
    <div slot="header" class="clearfix">
      <!-- 上部表单 -->
      <el-form :inline="true" :model="formInline" class="demo-form-inline">
        <el-row :gutter="10">
          <el-col :span="6">
            <el-form-item label="标签名称">
              <el-input
                v-model="formInline.user"
                placeholder="请输入"
              ></el-input>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="城市">
              <el-select v-model="formInline.region" placeholder="请选择">
                <el-option label="区域一" value="shanghai"></el-option>
                <el-option label="区域二" value="beijing"></el-option>
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="6">
            <el-form-item label="地区">
              <el-select v-model="formInline.region" placeholder="活动区域">
                <el-option label="区域一" value="shanghai"></el-option>
                <el-option label="区域二" value="beijing"></el-option>
              </el-select> </el-form-item
          ></el-col>
          <el-col :span="6">
            <el-form-item label="企业简称">
              <el-input
                v-model="formInline.user"
              ></el-input>
            </el-form-item>
          </el-col>
        </el-row>
      </el-form>
    </div>
    <!-- 卡片内容 -->
    <div class="text item"></div>
  </el-card>
</template>

<script>
export default {
  created () { },
  data () {
    return {
      formInline: {
        user: '',
        region: ''
      }
    }
  },
  methods: {},
  computed: {},
  watch: {},
  filters: {},
  components: {}
}
</script>

<style scoped lang='less'>
.text {
  font-size: 14px;
}

.item {
  margin-bottom: 18px;
}

.clearfix:before,
.clearfix:after {
  display: table;
  content: "";
}
.clearfix:after {
  clear: both;
}

.box-card {
  margin-top: 20px;
  margin-left: 20px;
  margin-right: 20px;
}
</style>
