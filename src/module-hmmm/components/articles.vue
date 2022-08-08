<template>
  <div>
    <el-card>
      <el-form :inline="true" :model="formInline" class="demo-form-inline">
        <el-form-item label="关键字">
          <el-input v-model="formInline.search" placeholder="根据文章标题搜索" class="search"></el-input>
        </el-form-item>
        <el-form-item label="状态">
          <el-select v-model="formInline.status" placeholder="请选择">
            <el-option v-for="(item, i) in status" :key="i" :label="item.label" :value="item.value"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item>
          <el-button @click="clearFn">清除</el-button>
          <el-button type="primary" @click="onSubmit">查询</el-button>
        </el-form-item>
        <el-form-item style="float:right">
          <el-button type="success" class="el-icon-edit" @click="showDialog = true;isEdit = false">新增技巧</el-button>
        </el-form-item>
      </el-form>
      <el-alert :title="'一共有' + total + '条数据'" type="info" show-icon></el-alert>
      <el-table :data="listData">
        <el-table-column label="序号" type="index" width="80px"></el-table-column>
        <el-table-column label="文章标题" prop="title"></el-table-column>
        <el-table-column label="阅读数" prop="visits"></el-table-column>
        <el-table-column label="录入人" prop="username"></el-table-column>
        <el-table-column label="录入时间" prop="createTime" :formatter="dateFormate"></el-table-column>
        <el-table-column label="状态" prop="state">
          <template v-slot="scope">
            {{ scope.row.state == 1 ? '已启用' : '已禁用' }}
          </template>
        </el-table-column>
        <el-table-column label="操作" prop="state">
          <template v-slot="scope">
            <el-button type="text" @click="preview(scope.row)">预览</el-button>
            <el-button type="text" @click="disabled(scope.row)">{{scope.row.state === 0 ? '启用' : '禁用'}}</el-button>
            <el-button type="text" :disabled="scope.row.state === 0" @click="Modify(scope.row)">修改</el-button>
            <el-button type="text" :disabled="scope.row.state === 0" @click="Delete(scope.row.id)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <div class="block" style="float:right">
        <el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange" background
          :page-sizes="[5, 10, 20, 50]" :page-size="data.pageSize" layout="prev , pager, next , sizes , jumper"
          :total="total">
        </el-pagination>
      </div>
    </el-card>

    <!-- dialog -->
    <el-dialog :visible.sync="showDialog" :title="isEdit ? '修改文章':'新增文章'" label-width="80px">
      <el-form :model="articleForm" :rules="articleRules" ref="articleForm">
        <el-form-item label="文章标题" prop="title">
          <el-input placeholder="请输入文章标签" class="input_width" v-model="articleForm.title"></el-input>
        </el-form-item>
        <el-form-item label="文章内容" prop="articleBody">
          <!-- 富文本编辑器 vue-quill-editor -->
            <quill-editor v-model="articleForm.articleBody" ref="myQuillEditor" :options="editorOption" @blur="onEditorBlur($event)"
            @focus="onEditorFocus($event)" @change="onEditorChange($event)" @ready="onEditorReady($event)">
          </quill-editor>
        </el-form-item>
        <el-form-item label="视频地址" class="videoURL" prop="videoURL">
          <el-input placeholder="请输入视频地址" class="input_width" v-model="articleForm.videoURL"></el-input>
        </el-form-item>
      </el-form>
      <template #footer>
        <el-button @click="cancelFn">取消</el-button>
        <el-button type="primary" @click="confirm">确 定</el-button>
      </template>
    </el-dialog>

    <!-- 预览dialog -->
    <el-dialog title="预览文章" :visible.sync="showVisible" class="preview">
      <h2>{{title}}</h2>
      <span>{{time}}</span>
      <span>{{username}}</span>
      <i class="el-icon-view"></i>
      <span>{{visits}}</span>
      <el-alert :title="articleBody" class="preview_alert"></el-alert>
    </el-dialog>
  </div>
</template>

<script>
import { list, list1, add, changeState, remove } from '@/api/hmmm/articles'
import { status } from '@/api/hmmm/constants'
import dayjs from 'dayjs'
export default {
  created () {
    this.getList()
  },
  data () {
    return {
      // 富文本编辑器配置
      editorOption: {
        modules: {
          toolbar: [
            ['bold', 'italic', 'underline', 'strike'], // 加粗 斜体 下划线 删除线
            ['blockquote', 'code-block'], // 引用  代码块
            [{ header: 1 }, { header: 2 }], // 1、2 级标题
            ['image', 'videoURL'] // 链接、图片、视频
          ]
        }
      },
      formInline: {
        search: '',
        status: ''
      },
      data: {
        page: 1,
        pagesize: 10
      },
      listData: [],
      status: status,
      total: null,
      showDialog: false,
      articleForm: { // 文章表单
        title: '',
        articleBody: '',
        videoURL: ''
      },
      articleRules: {
        title: [
          { required: true, message: '请输入文章标题', trigger: 'blur' }
        ],
        articleBody: [
          { required: true, message: '请输入文章内容', trigger: 'blur' }
        ]
      },
      isEdit: false,
      showVisible: false,
      title: '',
      time: '',
      username: '',
      visits: 0,
      articleBody: ''
    }
  },
  methods: {
    async onSubmit () {
      const res = await list1({
        keyword: this.formInline.search,
        state: this.formInline.status,
        page: this.data.page,
        pagesize: this.data.pagesize
      })
      this.listData = res.data.items
      this.clearFn()
    },
    async getList () {
      const res = await list(this.data)
      this.listData = res.data.items
      this.total = res.data.counts
    },
    // dayjs格式化时间
    dateFormate (row, column, cellValue, index) {
      return dayjs(cellValue).format('YYYY-MM-DD HH:mm:ss')
    },
    // 分页
    handleSizeChange (val) {
      this.data.pagesize = val
      this.getList()
    },
    handleCurrentChange (val) {
      this.data.page = val
      this.getList()
    },
    // 清除搜索条件
    clearFn () {
      this.formInline.search = ''
      this.formInline.status = ''
    },
    // 富文本编辑器
    // 失去焦点事件
    onEditorBlur (quill) {
      console.log('editor blur!', quill)
    },
    // 获得焦点事件
    onEditorFocus (quill) {
      console.log('editor focus!', quill)
    },
    // 准备富文本编辑器
    onEditorReady (quill) {
      console.log('editor ready!', quill)
    },
    // 内容改变事件
    onEditorChange ({ quill, html, text }) {
      console.log('editor change!', quill, html, text)
      this.articleForm.articleBody = html
    },
    // 取消
    cancelFn () {
      this.showDialog = false
      this.$refs.articleForm.resetFields()
    },
    // 确定
    async confirm () {
      const res = await add(this.articleForm)
      if (res.status === 200) {
        this.$message.success('添加成功')
        this.showDialog = false
        this.$refs.articleForm.resetFields()
        this.getList()
      } else {
        this.$message.error(res.msg)
      }
    },
    // 禁用方法
    async disabled (row) {
      console.log(row)
      // 如果当前row.state为1，发送请求传id禁用，否则发送请求传id启用
      const res = await changeState({
        id: row.id,
        state: row.state === 1 ? 0 : 1
      })
      if (res.status === 200) {
        this.$message.success('操作成功')
        this.getList()
      } else {
        this.$message.error(res.msg)
      }
    },
    // 修改文章
    async Modify (row) {
      this.isEdit = true
      this.showDialog = true
      console.log(row)
      this.articleForm = {
        title: row.title,
        articleBody: row.articleBody,
        videoURL: row.videoURL
      }
    },
    // 删除文章
    async Delete (id) {
      console.log(id)
      const res = await remove({
        id
      })
      if (res.status === 200) {
        this.$message.success('删除成功')
        this.getList()
      } else {
        this.$message.error(res.msg)
      }
    },
    // 预览文章
    preview (row) {
      this.showVisible = true
      this.title = row.title
      this.time = dayjs(row.createTime).format('YYYY-MM-DD HH:mm:ss')
      this.username = row.username
      this.visits = row.visits
      // 创建正则匹配p标签中的内容
      const reg = /<p>([\s\S]*?)<\/p>/g
      this.articleBody = reg.exec(row.articleBody)[1]
      console.log(this.articleBody)
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
  margin: 15px;
}

.el-input {
  width: 200px;
  height: 32px;
}

.el-alert {
  margin-bottom: 20px;
}

::v-deep .el-table th {
  background-color: #fafafa !important;
}

::v-deep .el-table th.el-table__cell>.cell {
  display: inline-block;
  box-sizing: border-box;
  position: relative;
  vertical-align: middle;
  padding-left: 10px;
  padding-right: 10px;
  width: 100%;
  font-size: 18px;
}

.el-table {
  margin-bottom: 10px;
}

.block {
  margin-bottom: 20px;
  margin-top: 10px;
}
.quill-editor {
  box-sizing: articleBody-box;
  width: 650px;
  height: 200px;
  padding-left: 77px;
}
.videoURL {
  margin-top: 70px;
  padding-left: 7px;
}
.input_width {
  width: 650px;
}

.preview {
  span {
    padding-right: 5px;
    font-size: 18px;
    display: inline-block;
  }
  i {
    font-size: 18px;
    padding-right: 5px;
  }
}
::v-deep .preview_title {
  width: 760px;
  height: 64px;
  padding-top: 20px;
}
</style>
