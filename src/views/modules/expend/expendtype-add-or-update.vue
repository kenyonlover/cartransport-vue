<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="支出类型编码" prop="code">
      <el-input v-model="dataForm.code" placeholder="支出类型编码" disabled="true"></el-input>
    </el-form-item>
    <el-form-item label="支出类型名称" prop="name">
      <el-input v-model="dataForm.name" placeholder="支出类型名称"></el-input>
    </el-form-item>
    <el-form-item label="备注" prop="note">
      <el-input v-model="dataForm.note" placeholder="备注"></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          expendTypeId: 0,
          code: '',
          name: '',
          note: ''
        },
        dataRule: {
          code: [
            { required: false, message: '支出类型编码不能为空', trigger: 'blur' }
          ],
          name: [
            { required: true, message: '支出类型名称不能为空', trigger: 'blur' }
          ],
          note: [
            { required: false, message: '备注不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.expendTypeId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.expendTypeId) {
            this.$http({
              url: this.$http.adornUrl(`/expend/expendtype/info/${this.dataForm.expendTypeId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.code = data.expendType.code
                this.dataForm.name = data.expendType.name
                this.dataForm.note = data.expendType.note
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/expend/expendtype/${!this.dataForm.expendTypeId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'expendTypeId': this.dataForm.expendTypeId || undefined,
                'code': this.dataForm.code,
                'name': this.dataForm.name,
                'note': this.dataForm.note
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>
