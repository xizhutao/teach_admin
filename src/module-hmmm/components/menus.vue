<template>
  <div>
    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <el-button style="float: right; padding: 3px 0" type="success"
          >菜单按钮</el-button
        >
      </div>
      <el-table :data="tableData" style="width: 100%" border>
        <el-table-column prop="" label="标题">

        </el-table-column>
        <el-table-column prop="name" label="权限点代码"> </el-table-column>
        <el-table-column prop="address" label="操作"> </el-table-column>
      </el-table>
    </el-card>
  </div>
</template>

<script>
import { list } from '@/api/base/menus'
export default {
  created () { this.getUserList() },
  data () {
    return {
      tableData: [{}]
    }
  },
  methods: {
    async getUserList () {
      try {
        const { data } = await list({
        })
        console.log(data)
        this.tableData = data.childs
      } catch (err) {
        console.log(err)
      }
    }
  },
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
.clearfix:before,
.clearfix:after {
  display: table;
  content: "";
}
.clearfix:after {
  clear: both;
}
.box-card {
  margin: 50px;
}
</style>
