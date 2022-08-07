<template>
  <div class="substance">
    <!-- 头部搜索，新增 -->
    <div class="title">
      <div class="left">
        学科名称<el-input></el-input>
        <el-button size="small">清除</el-button>
        <el-button type="primary" size="small">搜索</el-button>
      </div>
      <div class="right">
        <el-button type="success" icon="el-icon-edit" size="small"
          >新增学科</el-button
        >
      </div>
    </div>
    <!-- 中间数据显示条 -->
    <div class="center">
      <el-alert title="数据一共23条" type="info" show-icon :closable="false">
      </el-alert>
    </div>
    <!-- 表格数据 -->
    <el-table :data="tableData" style="width: 100%">
      <el-table-column type="index" label="序号" width="50"></el-table-column>
      <el-table-column prop="date" label="学科名称" width="100">
      </el-table-column>
      <el-table-column prop="date" label="创建者" width="130">
      </el-table-column>
      <el-table-column prop="date" label="创建日期" width="120">
      </el-table-column>
      <el-table-column prop="date" label="前台是否显示" width="120">
      </el-table-column>
      <el-table-column prop="date" label="二级目录" width="120">
      </el-table-column>
      <el-table-column prop="date" label="标签" width="120"> </el-table-column>

      <el-table-column prop="name" label="题目数量" width="120">
      </el-table-column>
      <el-table-column label="操作">
        <el-button size="small" type="text">学科分类</el-button>
        <el-button size="small" type="text">学科标签</el-button>
        <el-button size="small" type="text">修改</el-button>
        <el-button size="small" type="text">删除</el-button>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
export default {
  created () { },
  data () {
    return {
      input: '',
      tableData: [{
        date: '2016-05-02',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1518 弄'
      }, {
        date: '2016-05-04',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1517 弄'
      }, {
        date: '2016-05-01',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1519 弄'
      }, {
        date: '2016-05-03',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1516 弄'
      }]
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
.substance {
  background-color: #fff;
  padding: 20px;
  margin-bottom: 10px;
  .title {
    font-size: 14px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    .left {
      .el-input {
        width: 200px;
        margin-left:10px;
      }
      .el-button {
        margin-left: 10px;
      }
    }
  }
  .center {
    margin: 20px 0;
  }
  .el-table-column {
    .el-button {
      border: 0;
    }
  }
}
</style>
