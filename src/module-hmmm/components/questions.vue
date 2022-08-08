<template>
  <!-- 基础题库 -->
  <div class="questions">
    <el-card>
      <QuestionsSubject
        ref="QuestionsSubject"
        @testList="testList = $event"
        :page="page"
        :pagesize="pagesize"
      ></QuestionsSubject>

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
        <el-table-column label="录入时间" width="180">
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
            <el-button
              icon="el-icon-view"
              circle
              type="primary"
              size="small"
              @click="look(scope.row)"
            ></el-button>
            <el-button icon="el-icon-edit" circle type="success" size="small"
            @click="$router.push({name:'questions-new',params:scope.row})"></el-button>
            <el-button
              icon="el-icon-delete"
              circle
              type="danger"
              size="small"
              @click="clickDelete(scope.row) "
            ></el-button>
            <el-button icon="el-icon-check" circle type="warning" size="small" @click="addSelect(scope.row)"></el-button>
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
        :total="testList.counts"
      ></el-pagination>

      <!-- 弹出层 -->
      <el-dialog title="题目预览" :visible.sync="dialogVisible" width="60%" @close="dialogClose">
        <el-row class="top-message">
          <el-col
            :span="6"
          >【题型】：{{subjectDetails.questionType === '1' ? "单选题" :subjectDetails.questionType === '2' ? '多选题' : '简答题'}}</el-col>
          <el-col :span="6">【编号】：{{subjectDetails.id}}</el-col>
          <el-col
            :span="6"
          >【难度】：{{subjectDetails.difficulty === '1' ? "简单" :subjectDetails.questionType === '2' ? '一般' : '困难'}}</el-col>
          <el-col :span="6">
            【标签】：
            {{subjectDetails.tags}}
          </el-col>
          <el-col :span="6">【学科】：{{subjectDetails.subjectName}}</el-col>
          <el-col :span="6">【目录】：{{subjectDetails.directoryName}}</el-col>
          <el-col :span="6">【方向】：{{subjectDetails.direction}}</el-col>
        </el-row>
        <hr />
        <!-- 多选框 -->
        <template>
          <p>【题干】：</p>
          <p v-html="subjectDetails.question" style="color:blue"></p>
          <p>
            {{subjectDetails.questionType === '1' ? "单选题" :subjectDetails.questionType === '2' ? '多选题' : '简答题'}}
            选项：（以下选中的选项为正确答案）
          </p>
          <!-- 多选框 -->
          <el-checkbox-group v-model="checkList">
            <div v-for="item in subjectDetails.options" :key="item.id" style="margin-top:15px">
              <el-checkbox :label="item.title" :disabled="true" :checked="item.isRight === 1"></el-checkbox>
            </div>
          </el-checkbox-group>
        </template>
        <hr />

        <p>
          【参考答案】：
          <el-button type="danger" @click="videoShow=true">视频答案预览</el-button>
        </p>
        <video
          :src="subjectDetails.videoURL"
          v-if="videoShow"
          controls="controls"
          style="width:400px;height:300px"
        ></video>
        <hr />
        <p>【答案解析】：{{str}}</p>
        <hr />
        <p>【题目备注】：{{subjectDetails.remarks}}</p>
        <span slot="footer" class="dialog-footer">
          <el-button type="primary" @click="dialogVisible = false">关闭</el-button>
        </span>
      </el-dialog>
    </el-card>
  </div>
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
