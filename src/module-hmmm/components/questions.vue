<template>
  <!-- 基础题库 -->
  <div class="questions">
    <el-card>
      <QuestionsSubject ref="QuestionsSubject" @testList="testList = $event" :page="page" :pagesize="pagesize">
      </QuestionsSubject>

      <el-table :data="testList.items" stripe style="width: 100%">
        <el-table-column prop="number" label="试题编号"></el-table-column>
        <el-table-column prop="subject" label="学科"></el-table-column>
        <el-table-column prop="catalog" label="目录"></el-table-column>
        <el-table-column label="题型">
          <template
            v-slot="scope"
          >{{scope.row.questionType === '1' ? '单选' : scope.row.questionType === '2'?'多选' :'简答'}}</template>
        </el-table-column>
        <el-table-column label="题干" width="280">
          <template v-slot="scope">
            <div v-html="scope.row.question"></div>
          </template>
        </el-table-column>
        <el-table-column label="录入时间">
          <template v-slot="scope">{{scope.row.addDate | parseTimeByString}}</template>
        </el-table-column>
        <el-table-column label="难度">
          <template
            v-slot="scope"
          >{{scope.row.difficulty === '1' ? '简单' : scope.row.questionType === '2'?'一般' :'困难'}}</template>
        </el-table-column>
        <el-table-column prop="creator" label="录入人"></el-table-column>
        <el-table-column label="操作" width="180">
          <template v-slot="scope">
            <el-button icon="el-icon-view" circle type="primary" size="small" @click="look(scope.row)"></el-button>
            <el-button icon="el-icon-edit" circle type="success" size="small"></el-button>
            <el-button icon="el-icon-delete" circle type="danger" size="small" @click="clickDelete(scope.row) "></el-button>
            <el-button icon="el-icon-check" circle type="warning" size="small"></el-button>
          </template>
        </el-table-column>
      </el-table>

      <!-- 分页器 -->
       <el-pagination
       style="margin-top:20px"
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :page-sizes="[1,5, 10, 20, 50]"
      :page-size="pagesize"
      layout="total, sizes, prev, pager, next, jumper"
      :total="testList.counts">
    </el-pagination>
    </el-card>
  </div>
</template>

<script>
import QuestionsSubject from './questions-subject.vue'
import { remove as questionsRemove } from '@/api/hmmm/questions'
export default {
  name: 'Questions',
  created () {
  },
  data () {
    return {
      testList: [],
      page: 1,
      pagesize: 5
    }
  },
  methods: {
    handleSizeChange (val) {
      this.pagesize = val
    },
    handleCurrentChange (val) {
      this.page = val
    },
    look (obj) {
      console.log(obj)
    },
    clickDelete (obj) {
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(async () => {
        try {
          await questionsRemove(obj)
          this.$message({
            type: 'success',
            message: '删除成功!'
          })
          this.$refs.QuestionsSubject.search()
        } catch (err) {
          console.log(err)
        }
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        })
      })
    }
  },
  computed: {},
  watch: {},
  filters: {},
  components: {
    QuestionsSubject
  }
}
</script>

<style scoped lang='less'>
.questions {
  padding-top: 10px;
  padding-left: 10px;
}
</style>
