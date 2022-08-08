<template>
  <el-card>
    <div slot="header" class="clearfix">
      <span>试题录入</span>
    </div>
    <el-form :model="form" :rules="rules" ref="form">
      <!-- 学科 -->
      <el-form-item label="学科:" prop="subjectID" label-width="120px">
        <el-select
          v-model="form.subjectID"
          placeholder="请选择"
          @change="subjectChange"
          style="width: 400px"
        >
          <el-option
            v-for="item in subjectOptions"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          ></el-option>
        </el-select>
      </el-form-item>
      <!-- 目录 -->
      <el-form-item label="目录:" prop="catalogID" label-width="120px">
        <el-select
          v-model="form.catalogID"
          placeholder="请选择"
          @change="catalogChange"
          style="width: 400px"
        >
          <el-option
            v-for="item in directoryOptions"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          ></el-option>
        </el-select>
      </el-form-item>
      <!-- 企业 -->
      <el-form-item label="企业:" prop="enterpriseID" label-width="120px">
        <el-select
          v-model="form.enterpriseID"
          placeholder="请选择"
          style="width: 400px"
          @change="enterpriseChange"
        >
          <el-option
            v-for="item in companyOptions"
            :key="item.id"
            :label="item.company"
            :value="item.id"
          ></el-option>
        </el-select>
      </el-form-item>
      <!-- 城市 -->
      <el-form-item label="城市:" prop="city" label-width="120px">
        <el-select
          v-model="form.province"
          placeholder="请选择"
          @change="provinceChange"
          style="width: 198px; margin-right: 4px"
        >
          <el-option
            v-for="(item, index) in provinceOptions"
            :key="index"
            :label="item"
            :value="item"
          ></el-option>
        </el-select>
        <el-select
          v-model="form.city"
          placeholder="请选择"
          style="width: 198px"
          @change="cityChange"
        >
          <el-option
            v-for="(item, index) in cityOptions"
            :key="index"
            :label="item"
            :value="item"
          ></el-option>
        </el-select>
      </el-form-item>
      <!-- 方向 -->
      <el-form-item label="方向" prop="direction" label-width="120px">
        <el-select
          v-model="form.direction"
          placeholder="请选择"
          style="width: 400px"
        >
          <el-option
            v-for="(item, index) in direction"
            :key="index"
            :label="item"
            :value="item"
          ></el-option>
        </el-select>
      </el-form-item>
      <!-- 题型 -->
      <el-form-item label="题型:" prop="questionType" label-width="120px">
        <el-radio v-model="form.questionType" label="1">单选</el-radio>
        <el-radio v-model="form.questionType" label="2">多选</el-radio>
        <el-radio v-model="form.questionType" label="3">简答</el-radio>
      </el-form-item>
      <!-- 难度 -->
      <el-form-item label="难度:" prop="difficulty" label-width="120px">
        <el-radio v-model="form.difficulty" label="1">简单</el-radio>
        <el-radio v-model="form.difficulty" label="2">一般</el-radio>
        <el-radio v-model="form.difficulty" label="3">困难</el-radio>
      </el-form-item>
      <!-- 题干 -->
      <el-form-item
        label="题干:"
        prop="question"
        label-width="120px"
        class="text"
      >
        <quill-editor
          v-model="form.question"
          ref="myQuillEditor"
          :options="editorOption"
          @blur="onEditorBlur($event)"
          @focus="onEditorFocus($event)"
          @change="onEditorChange($event)"
        >
        </quill-editor>
      </el-form-item>
      <!-- 选项 -->
      <el-form-item label="选项:" label-width="120px">
        <!-- 单选 -->
        <div class="radio" v-if="form.questionType === '1'">
          <div
            class="choice"
            v-for="(item, index) in form.options"
            :key="index"
          >
            <el-radio :label="index" v-model="radio" @change="change"
              >{{ item.code + ":" }}
              <el-input class="input" v-model="item.title"></el-input>
              <el-upload
                class="avatar-uploader"
                :action="item.img"
                :show-file-list="false"
                :on-success="handleAvatarSuccess"
                :before-upload="beforeAvatarUpload"
              >
                上传图片
                <i class="el-icon-circle-close"></i>
              </el-upload>
            </el-radio>
          </div>
        </div>
        <!-- 多选 -->
        <div class="checkbox" v-else-if="form.questionType === '2'">
          <div
            class="choice"
            v-for="(item, index) in form.options"
            :key="index"
          >
            <el-checkbox :label="index" v-model="checkbox"
              >{{ item.code + ":" }}
              <el-input class="input" v-model="item.title"></el-input>
              <el-upload
                class="avatar-uploader"
                :action="item.img"
                :show-file-list="false"
                :on-success="handleAvatarSuccess"
                :before-upload="beforeAvatarUpload"
              >
                上传图片
                <i class="el-icon-circle-close"></i>
              </el-upload>
            </el-checkbox>
          </div>
        </div>
        <el-button
          type="danger"
          :disabled="form.questionType != 2"
          @click="addOptions"
          >+增加选项与答案</el-button
        >
      </el-form-item>
      <!-- 解析视频 -->
      <el-form-item label="解析视频:" label-width="120px">
        <el-input style="width: 400px" v-model="form.videoURL"></el-input>
      </el-form-item>
      <!-- 答案解析 -->
      <el-form-item
        label="答案解析:"
        prop="answer"
        label-width="120px"
        class="text"
      >
        <quill-editor
          v-model="form.answer"
          ref="myQuillEditor"
          :options="editorOption"
          @blur="onEditorBlur($event)"
          @focus="onEditorFocus($event)"
          @change="onEditorChange($event)"
        >
        </quill-editor>
      </el-form-item>
      <!-- 题目备注 -->
      <el-form-item label="题目备注:" label-width="120px">
        <el-input type="textarea" v-model="form.remarks"></el-input>
      </el-form-item>
      <!-- 试题标签 -->
      <el-form-item label="试题标签:" label-width="120px">
        <el-select
          v-model="form.tags"
          placeholder="请选择试题标签"
          style="width: 400px"
        >
          <el-option
            v-for="item in tagsOptions"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          ></el-option
        ></el-select>
      </el-form-item>
      <el-form-item label-width="120px">
        <el-button type="primary" @click="submit">确认提交</el-button>
      </el-form-item>
    </el-form>
  </el-card>
</template>

<script>
import { add } from '@/api/hmmm/questions'
import { simple as tagsList } from '@/api/hmmm/tags'
import { simple } from '@/api/hmmm/subjects'
import { simple as directory } from '@/api/hmmm/directorys'
import { list } from '@/api/hmmm/companys'
import { provinces, citys } from './citys'
import { direction } from '@/api/hmmm/constants'
// 富文本
import { quillEditor } from 'vue-quill-editor'

export default {
  created () {
    this.getSubject()
    this.getCompany()
    this.getProvince()
    this.direction = direction
  },

  data () {
    return {
      form: {
        // 学科
        subjectID: '',
        // 目录
        catalogID: '',
        // 企业
        enterpriseID: '',
        // 市
        province: '',
        // 区
        city: '',
        // 方向
        direction: '',
        // 题型
        questionType: '1',
        // 难度
        difficulty: '1',
        // 选项
        options: [
          { isRight: false, code: 'A', title: '', img: '' },
          { isRight: false, code: 'B', title: '', img: '' },
          { isRight: false, code: 'C', title: '', img: '' },
          { isRight: false, code: 'D', title: '', img: '' }
        ],
        // 题干
        question: '',
        // 解析视频
        videoURL: '',
        // 答案解析
        answer: '',
        // 题目备注
        remarks: '',
        // 标签
        tags: ''
      },
      rules: {
        subjectID: [
          { required: true, message: '请选择学科', trigger: 'change' }
        ],
        catalogID: [
          { required: true, message: '请选择目录', trigger: 'change' }

        ],
        enterpriseID: [
          { required: true, message: '请选择企业', trigger: 'change' }
        ],
        city: [
          { required: true, message: '请选择地区', trigger: 'change' }
        ],
        direction: [
          { required: true, message: '请选择方向', trigger: 'change' }
        ],
        questionType: [
          { required: true, message: '请选择', trigger: 'change' }
        ],
        difficulty: [
          { required: true, message: '请选择', trigger: 'change' }
        ],
        question: [
          { required: true, message: '请输入题干', trigger: 'blur' }
        ],
        answer: [
          { required: true, message: '请输入题干', trigger: 'blur' }
        ]
      },
      content: '',
      subjectOptions: [],
      directoryOptions: [],
      companyOptions: [],
      provinceOptions: [],
      cityOptions: [],
      tagsOptions: [],
      // 单选
      radio: null,
      // 多选
      checkbox: [],
      // 富文本工具栏
      editorOption: {
        modules: {
          toolbar: [
            ['bold', 'italic', 'underline', 'strike'], // 加粗，斜体，下划线，删除线
            [{ list: 'ordered' }, { list: 'bullet' }], // 有序列表，无序列表
            ['blockquote', 'code-block'], // 引用，代码块
            // ['clean'] // 清除样式
            ['image', 'link'] // 上传图片、上传链接
          ]
        }
      },
      arr: ['E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q'],
      index: '0'
    }
  },
  methods: {
    // 学科
    async getSubject () {
      const res = await simple()
      // console.log(res)
      this.subjectOptions = res.data
    },
    // 学科值发生变化时触发
    subjectChange (val) {
      this.form.subjectID = val
      // 改变时清空目录
      this.form.catalogID = ''
      this.directoryOptions = []
      this.getDirectory()
      this.$refs.form.validateField(['catalogID'], async valid => {
        if (!valid) {
          this.$refs.form.clearValidate()
        }
      })
      console.log(this.form.subjectID)
      this.$nextTick(() => {
        this.getTagList()
      })
    },
    // 目录
    async getDirectory () {
      const res = await directory({ subjectID: this.form.subjectID })
      // console.log(res)
      this.directoryOptions = res.data
    },
    // 目录值发生变化时触发
    catalogChange (val) {
      // console.log('val', val)
      this.form.catalogID = val
      console.log(this.form.catalogID)
    },
    // 企业
    async getCompany () {
      const res = await list()
      // console.log(res)
      this.companyOptions = res.data.items
    },
    // 企业值发生变化时触发
    enterpriseChange (val) {
      // console.log('val', val)
      this.form.enterpriseID = val
      console.log(this.form.enterpriseID)
    },
    // 城市
    getProvince () {
      this.provinceOptions = provinces()
      console.log(this.form.province)
    },
    // 市值发生变化时触发
    provinceChange (val) {
      console.log(val)
      this.cityOptions = citys(val)
      // console.log('city', this.cityOptions)
    },
    // 区值发生变化时触发
    cityChange () {
      console.log(this.form.city)
    },
    handleAvatarSuccess (res, file) { },
    beforeAvatarUpload (file) {

    },
    // 富文本失去焦点事件
    onEditorBlur () {
      this.$refs.form.validateField(['question', 'answer'], async valid => {
        if (!valid) {
          this.$refs.form.clearValidate()
        }
      })
    },
    onEditorFocus () {
      // 获得焦点事件
    },
    onEditorChange () {
      // 内容改变事件
    },
    // 获取试题标签
    async getTagList () {
      const res = await tagsList({ subjectID: this.form.subjectID })
      console.log('res', res)
      this.tagsOptions = res.data
    },
    // 单选框值改变时
    change (val) {
      console.log(val)
      this.form.options.forEach(item => {
        item.isRight = false
      })
      console.log(this.form.options)
      this.$set(this.form.options[val], 'isRight', true)
    },
    // 提交表单
    async submit () {
      this.$refs.form.validate(async (valid) => {
        if (!valid) {
        }
        this.form.tags = this.form.tags.toString()
        await add({ ...this.form })
        this.$message.success('录入成功')
        this.$refs.form.resetFields()
      })
    },
    addOptions () {
      this.form.options.push({ isRight: false, code: this.arr[this.index], title: '', img: '' })
      this.index++
      // console.log(this.index)
    }

  },
  computed: {

  },
  watch: {},
  filters: {},
  components: { quillEditor }
}
</script>

<style scoped lang='less'>
.el-card {
  margin: 20px;
}
.clearfix:before,
.clearfix:after {
  display: table;
  content: "";
}
.clearfix:after {
  clear: both;
}
.text {
  /deep/.el-form-item__content {
    height: 280px;
  }
  .quill-editor {
    height: 230px;
  }
}

.radio {
  .choice {
    display: flex;
    justify-content: space-between;
    align-items: center;
    height: 80px;
    .input {
      margin-left: 7px;
    }
    .el-radio {
      display: flex;
      align-items: center;
    }
    /deep/.el-radio__label {
      display: flex;
      align-items: center;
      height: 80px;
    }
    .avatar-uploader {
      position: relative;
      display: block;
      text-align: center;
      width: 145px;
      height: 60px;
      line-height: 60px;
      border: 1px dashed #d9d9d9;
      border-radius: 6px;
      margin-left: 4px;
      color: #303133;
      font-size: 14px;
    }
    .el-icon-circle-close {
      position: absolute !important;
      top: -5px;
      right: -7px;
      font-size: 18px;
      color: #999 !important;
    }
  }
}
.checkbox {
  .choice {
    display: flex;
    justify-content: space-between;
    align-items: center;
    height: 80px;
    .input {
      margin-left: 7px;
    }
    .el-checkbox {
      display: flex;
      align-items: center;
    }
    /deep/.el-checkbox__label {
      display: flex;
      align-items: center;
      height: 80px;
    }
    .avatar-uploader {
      position: relative;
      display: block;
      text-align: center;
      width: 145px;
      height: 60px;
      line-height: 60px;
      border: 1px dashed #d9d9d9;
      border-radius: 6px;
      margin-left: 4px;
      color: #303133;
      font-size: 14px;
    }
    .el-icon-circle-close {
      position: absolute !important;
      top: -5px;
      right: -7px;
      font-size: 18px;
      color: #999 !important;
    }
  }
}
/deep/.el-textarea__inner {
  width: 400px;
  height: 90px;
}
</style>
