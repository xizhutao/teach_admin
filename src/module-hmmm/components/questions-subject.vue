<template>
  <!-- 题库管理-筛选 -->
  <div class="questions-subject">
    <!-- 顶部按钮 -->
    <el-row type="flex" class="top">
      <el-col class="instructions">说明：目前支持学科和关键字条件筛选</el-col>
      <el-col class="add-topic">
        <el-button type="success" icon="el-icon-edit" size="small">新增试题</el-button>
      </el-col>
    </el-row>

    <!-- 下拉框 -->
    <el-form label-width="80px" :model="subjectForm" :rules="subjectRules" ref="subjectFormRef">
      <el-row>
        <!-- 学科 -->
        <el-col :span="6">
          <el-form-item label="学科" prop="subjectID">
            <el-select v-model="subjectForm.subjectID" placeholder="请选择" @change="subjectChange">
              <el-option
                v-for="item in subjectList"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-form-item>
        </el-col>
        <!-- 二级目录 -->
        <el-col :span="6">
          <el-form-item label="二级目录" prop="directory">
            <el-select v-model="subjectForm.directory" placeholder="请选择">
              <template v-if="directorysList.length">
                <el-option
                  v-for="item in directorysList"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value"
                ></el-option>
              </template>
            </el-select>
          </el-form-item>
        </el-col>
        <!-- 标签 -->
        <el-col :span="6">
          <el-form-item label="标签" prop="tag">
            <el-select v-model="subjectForm.tag" placeholder="请选择"></el-select>
          </el-form-item>
        </el-col>
        <!-- 关键字 -->
        <el-col :span="6">
          <el-form-item label="关键字" prop="keyword">
            <el-input v-model="subjectForm.keyword" placeholder="根据题干搜索" style="width:223px"></el-input>
          </el-form-item>
        </el-col>
        <!-- 试题类型 -->
        <el-col :span="6">
          <el-form-item label="试题类型" prop="questionType">
            <el-select v-model="subjectForm.questionType" placeholder="请选择">
              <el-option
                v-for="item in questionTypeList"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-form-item>
        </el-col>
        <!-- 难度 -->
        <el-col :span="6">
          <el-form-item label="难度" prop="difficulty">
            <el-select v-model="subjectForm.difficulty" placeholder="请选择">
              <el-option
                v-for="item in difficulty"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-form-item>
        </el-col>
        <!-- 方向 -->
        <el-col :span="6">
          <el-form-item label="方向" prop="direction">
            <el-select v-model="subjectForm.direction" placeholder="请选择">
              <el-option
                v-for="(item,index) in directionList"
                :key="index"
                :label="item"
                :value="item"
              ></el-option>
            </el-select>
          </el-form-item>
        </el-col>
        <!-- 录入人 -->
        <el-col :span="6">
          <el-form-item label="录入人"  prop="creatorID">
            <el-select v-model="subjectForm.creatorID" placeholder="请选择">
              <el-option
                v-for="item in creatorList"
                :key="item.id"
                :label="item.username"
                :value="item.id"
              ></el-option>
            </el-select>
          </el-form-item>
        </el-col>
        <!-- 题目备注 -->
        <el-col :span="6">
          <el-form-item label="题目备注" prop="remarks">
            <el-input v-model="subjectForm.remarks" style="width:223px"></el-input>
          </el-form-item>
        </el-col>
        <!-- 企业简称 -->
        <el-col :span="6">
          <el-form-item label="企业简称" prop="shortName">
            <el-input v-model="subjectForm.shortName" style="width:223px"></el-input>
          </el-form-item>
        </el-col>
        <!-- 城市 -->
        <el-col :span="6">
          <el-form-item label="城市" prop="province">
            <el-select v-model="subjectForm.province" placeholder="请选择" style="width:110px;margin-right:5px">
              <el-option
                v-for="(item,index) in provinces"
                :key="index"
                :label="item"
                :value="item"
              ></el-option>
            </el-select>

            <el-select v-model="subjectForm.city" placeholder="请选择" style="width:110px">
              <el-option
                v-for="(item,index) in citys"
                :key="index"
                :label="item"
                :value="item"
              ></el-option>
            </el-select>
          </el-form-item>
        </el-col>
        <!-- 按钮 -->
        <el-col :span="6" class="subjectButton">
          <el-button @click="closeAll">清除</el-button>
          <el-button type="primary" @click="search">搜索</el-button>
        </el-col>
      </el-row>
    </el-form>

    <!-- 插槽 -->
    <slot></slot>

    <el-alert
    :title="`数据一共 ${counts} 条`"
    type="info"
    :closable="false"
    style="margin-bottom:20px"
    show-icon>
  </el-alert>
  </div>
</template>

<script>
import { simple as subjectsSimple } from '@/api/hmmm/subjects'
import { directorysList } from '@/api/hmmm/directorys'
import { simple as userSimple } from '@/api/base/users'
import { difficulty, questionType, direction } from '@/api/hmmm/constants'
import { provinces, citys } from '@/api/hmmm/citys'
import { list as questionsList } from '@/api/hmmm/questions'
import _ from 'lodash'

export default {
  name: 'QuestionsSubject',
  props: {
    page: {
      type: Number,
      default: 1
    },
    pagesize: {
      type: Number,
      default: 5
    }
  },
  created () {
    this.getSubjectsSimple()
    this.getUserSimple()
    this.search()
  },
  data () {
    return {
      subjectForm: {
        subjectID: '',
        directory: '',
        tag: '',
        keyword: '',
        questionType: '', // 类型
        difficulty: '', // 难度
        direction: '', // 方向
        creatorID: '', // 录入人
        remarks: '', // 题目备注
        shortName: '', // 企业名称
        province: '', // 城市
        city: '' // 地区
      },
      subjectRules: {},
      subjectList: [], // 学科列表
      creatorList: [], // 录入人列表
      directorysList: [], // 二级目录
      questionTypeList: questionType, // 类型
      difficulty: difficulty, // 难度
      directionList: direction, // 方向
      provinces: provinces(), // 城市
      citys: [], // 区列表
      counts: 0 // 数据条数
    }
  },
  methods: {
    // 获取学科列表
    async getSubjectsSimple () {
      try {
        const { data } = await subjectsSimple()
        console.log('学科列表', data)
        this.subjectList = data
      } catch (err) {
        console.log(err)
      }
    },
    // 获取录入人列表
    async getUserSimple () {
      try {
        const { data } = await userSimple()
        console.log('录入人', data)
        this.creatorList = data
      } catch (err) {
        console.log(err)
      }
    },
    // 根据学科获取目录
    async subjectChange () {
      try {
        const { data } = await directorysList({ subjectID: this.subjectForm.subjectID })
        console.log('根据学科获取目录', data)
        this.directorysList = data
      } catch (err) {
        console.log(err)
      }
    },
    // 清除
    closeAll () {
      this.$refs.subjectFormRef.resetFields()
      console.log(1)
    },
    // 搜索
    async search () {
      try {
        if (this.$route.name === 'questions-list') {
          this.subjectForm.page = this.page
          this.subjectForm.pagesize = this.pagesize
          const { data } = await questionsList(_.pickBy({ ...this.subjectForm }))
          console.log(data)
          this.counts = data.counts
          this.$emit('testList', data)
        }
      } catch (err) {
        console.log(err)
      }
    }
  },
  computed: {
  },
  watch: {
    'subjectForm.province': {
      handler: function (newVal, oldVal) {
        this.subjectForm.city = ''
        this.citys = citys(newVal)
      }
    },
    page () {
      this.search()
    },
    pagesize () {
      this.search()
    }
  },
  filters: {},
  components: {}
}
</script>

<style scoped lang='less'>
.questions-subject {
  .top {
    margin-bottom: 15px;
    height: 32px;
    .instructions {
      color: pink;
      font-size: 12px;
    }
    .add-topic {
      position: relative;
      .el-button {
        position: absolute;
        right: 0;
      }
    }
  }

  .subjectButton {
    display: flex;
    justify-content: right;
    padding-right: 12px;
  }
}
</style>
