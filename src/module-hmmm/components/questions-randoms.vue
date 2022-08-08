<template>
  <el-card>
    <div class="header">
      <el-form>
        <el-form-item label="关键字" prop="difficulty" label-width="80px">
          <el-input
            style="width: 240px; font-size: 13px"
            placeholder="根据编号搜索"
            clearable
          ></el-input>
        </el-form-item>
      </el-form>
      <div class="right">
        <el-button size="mini">清除</el-button>
        <el-button type="primary" size="mini">搜索</el-button>
      </div>
    </div>
    <el-alert title="数据一共条" type="info" show-icon :closable="false">
    </el-alert>
    <el-table :data="list">
      <el-table-column label="编号"> </el-table-column>
      <el-table-column label="题型"> </el-table-column>
      <el-table-column label="题目编号"> </el-table-column>
      <el-table-column label="录入时间"> </el-table-column>
      <el-table-column label="答题时间(s)"> </el-table-column>
      <el-table-column label="正确率(%)"> </el-table-column>
      <el-table-column label="录入人"> </el-table-column>
      <el-table-column label="操作"> </el-table-column>
    </el-table>
  </el-card>
</template>

<script>
// import { randoms } from '@/api/hmmm/questions'
export default {
  created () {
    this.getList()
  },
  data () {
    return {
      list: [],
      obj: {
        page: '1',
        pagesize: '20',
        keyword: null
      }
    }
  },
  methods: {
    async getList () {
      // const res = await randoms(this.obj)
      // console.log(res)
    }
  },
  computed: {},
  watch: {},
  filters: {},
  components: {}
}
</script>

<style scoped lang='less'>
.el-card {
  margin: 10px;
}
.header {
  display: flex;
  justify-content: space-between;
}
.el-alert {
  margin-bottom: 15px;
}
</style>
