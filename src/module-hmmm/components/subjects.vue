<template>
  <div class="substance">
    <!-- 头部搜索，新增 -->
    <div class="title">
      <div class="left">
        学科名称<el-input v-model="value"></el-input>
        <el-button size="small" @click="clear">清除</el-button>
        <el-button type="primary" size="small" @click="handleClick"
          >搜索</el-button
        >
      </div>
      <div class="right">
        <el-button
          type="success"
          icon="el-icon-edit"
          size="small"
          @click="addClick"
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
      <el-table-column
        type="index"
        label="序号"
        width="50"
        align="center"
      ></el-table-column>
      <el-table-column
        prop="subjectName"
        label="学科名称"
        width="100"
        align="center"
      >
      </el-table-column>
      <el-table-column
        prop="username"
        label="创建者"
        width="130"
        align="center"
      >
      </el-table-column>
      <el-table-column
        prop="addDate"
        label="创建日期"
        width="180"
        align="center"
      >
        <template v-slot="scope">
          {{ scope.row.addDate | dateformat }}
        </template>
      </el-table-column>
      <el-table-column
        prop="isFrontDisplay"
        label="前台是否显示"
        width="120"
        align="center"
        :formatter="formData"
      >
      </el-table-column>
      <el-table-column
        prop="twoLevelDirectory"
        label="二级目录"
        width="120"
        align="center"
      >
      </el-table-column>
      <el-table-column prop="tags" label="标签" width="120" align="center">
      </el-table-column>

      <el-table-column
        prop="totals"
        label="题目数量"
        width="120"
        align="center"
      >
      </el-table-column>
      <el-table-column label="操作" align="center">
        <template v-slot="scope">
          <el-button size="small" type="text">学科分类</el-button>
          <el-button size="small" type="text">学科标签</el-button>
          <el-button
            size="small"
            type="text"
            @click="modificationClick(scope.row)"
            >修改</el-button
          >
          <el-button size="small" type="text" @click="del(scope.row.id)"
            >删除</el-button
          >
        </template>
      </el-table-column>
    </el-table>
    <!-- 新增对话框 -->
    <el-dialog
      title="新增学科"
      :visible.sync="isAddShow"
      width="30%"
      @close="handleClose"
    >
      <el-form :model="rulesForm" :rules="rules" ref="rulesFormRef">
        <el-form-item label="学科名称" label-width="80px" prop="subjectName">
          <el-input
            placeholder="请输入学科名称"
            v-model="rulesForm.subjectName"
          ></el-input>
        </el-form-item>
        <el-form-item label="是否显示" label-width="80px">
          <el-switch
            v-model="rulesForm.isFrontDisplay"
            active-color="#13ce66"
            inactive-color="#ff4949"
            :active-value="1"
            :inactive-value="0"
          >
          </el-switch>
        </el-form-item>
      </el-form>
      <template #footer>
        <el-button @click="isAddShow = false">取消</el-button>
        <el-button type="primary" @click="add">确定</el-button>
      </template>
    </el-dialog>
  </div>
</template>

<script>
import { list, add, update, remove } from '@/api/hmmm/subjects'
export default {
  created () {
    this.getList()
  },
  data () {
    return {
      id: '', // 数据ID
      value: '', // 搜索框信息
      tableData: [], // 表单栏数据
      listObj: {
        subjectName: '', // 学科名称
        page: 1, // 显示页面
        pagesize: 9999 // 显示数量
      }, // 请求表单兰数据ajax
      isAddShow: false, // 新增弹出框
      rulesForm: {
        subjectName: '', // 新增表单名称
        isFrontDisplay: 1 // 新增表单是否显示
      }, // 新增表单数据
      rules: {
        subjectName: [{ required: true, message: '学科名称不能为空', trigger: 'blur' }]
      }, // 表单验证
      isModificationShow: false // 修改弹出框
    }
  },
  methods: {
    // 请求数据
    async getList () {
      const res = await list(this.listObj)
      console.log(res)
      this.tableData = res.data.items
    },
    // 格式化是否显示
    formData (row, column, cellValue, index) {
      if (row.isFrontDisplay === 1) {
        return '是'
      } else {
        return '否'
      }
    },
    // 点击搜索
    handleClick () {
      this.listObj.subjectName = this.value
      this.getList()
    },
    // 点击清除
    clear () {
      this.value = ''
    },
    // 新增学科
    addClick () {
      this.isModificationShow = false
      this.isAddShow = true
      this.rulesForm = {}
    },
    // 确定新增
    add () {
      this.$refs.rulesFormRef.validate(async bool => {
        if (!bool) this.$message.error('学科名称不能为空')
        if (this.isModificationShow) {
          console.log(this.id)
          await update({ id: this.id, subjectName: this.rulesForm.subjectName, isFrontDisplay: this.rulesForm.isFrontDisplay })
          await this.$message.success('操作成功')
          this.isAddShow = false
          this.isModificationShow = false
          this.getList()
        } else {
          console.log(this.rulesForm.isFrontDisplay)
          const res = await add(this.rulesForm)
          console.log(res)
          await this.$message.success('操作成功')
          this.isAddShow = false
          this.getList()
        }
      })
    },

    // 修改
    modificationClick (row) {
      this.isModificationShow = true
      this.isAddShow = true
      this.rulesForm = { ...row }
      this.id = row.id
    },
    // 表单重置
    handleClose () {
      this.$refs.rulesFormRef.resetFields()
    },
    // 删除
    async del (id) {
      try {
        await this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        })
        console.log(this.id)
        await remove({ id: id })
        this.$message({
          type: 'success',
          message: '删除成功!'
        })
        this.getList()
      } catch (err) {
        this.$message({
          type: 'info',
          message: '已取消删除'
        })
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
        margin-left: 10px;
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
