<template>
  <div>123</div>
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
