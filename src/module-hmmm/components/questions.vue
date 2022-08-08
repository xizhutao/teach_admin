<template>
  <div>123</div>
</template>

<script>
import QuestionsSubject from './questions-subject.vue'
import { remove as questionsRemove, detail, choiceAdd } from '@/api/hmmm/questions'
export default {
  name: 'Questions',
  created () {
  },
  data () {
    return {
      testList: [],
      page: 1,
      pagesize: 5,
      dialogVisible: false,
      subjectDetails: {},
      videoShow: false,
      str: '12'
    }
  },
  methods: {
    handleSizeChange (val) {
      this.pagesize = val
    },
    handleCurrentChange (val) {
      this.page = val
    },
    async look (obj) {
      console.log(obj)
      try {
        const { data } = await detail({ id: obj.id })
        console.log(data)
        this.subjectDetails = data
        this.dialogVisible = true
      } catch (err) {
        console.log(err)
      }
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
    },
    dialogClose () {
      this.videoShow = false
      this.subjectDetails = {}
    },
    addSelect (obj) {
      console.log(obj)
      this.$confirm('此操作将该题目加入精选, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'info'
      }).then(async () => {
        try {
          await choiceAdd({ id: obj.id, choiceState: 1 })

          this.$message({
            type: 'success',
            message: '添加成功!'
          })
          this.$refs.QuestionsSubject.search()
        } catch (err) {
          console.log(err)
        }
      }).catch(() => {})
    }
  },
  computed: {
    checkList () {
      if (this.subjectDetails.length) {
        return [...this.subjectDetails.options]
      } else {
        return []
      }
    }
  },
  watch: {
    dialogVisible () {
      if (this.dialogVisible && this.subjectDetails.answer) {
        this.str = this.subjectDetails.answer.replace(/<p>|<\/p>/g, '')
      }
    }
  },
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

  .top-message {
    .el-col {
      padding: 10px 0;
    }
  }

  .el-dialog {
    .is-disabled.is-checked {
      :deep(.el-checkbox__inner) {
        background-color: #409eff !important;
        border-color: #409eff !important;
      }
      :deep(.el-checkbox__label) {
        color: #409eff !important;
      }
    }
  }
}
</style>
