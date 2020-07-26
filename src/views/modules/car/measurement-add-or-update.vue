<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="计量单位编码" prop="measurementCode" label-width="20%">
      <el-input v-model="dataForm.measurementCode" placeholder="计量单位编码"></el-input>
    </el-form-item>
    <el-form-item label="计量单位名称" prop="measurementName" label-width="20%">
      <el-input v-model="dataForm.measurementName" placeholder="计量单位名称"></el-input>
    </el-form-item>
    <el-form-item label="备注" prop="note" label-width="20%">
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
          measurementId: 0,
          measurementCode: '',
          measurementName: '',
          note: '',
          deleted: ''
        },
        dataRule: {
          measurementCode: [
            { required: true, message: '计量单位编码不能为空', trigger: 'blur' }
          ],
          measurementName: [
            { required: true, message: '计量单位名称不能为空', trigger: 'blur' }
          ],
          note: [
            { required: false, message: '备注不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.measurementId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.measurementId) {
            this.$http({
              url: this.$http.adornUrl(`/car/measurement/info/${this.dataForm.measurementId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.measurementCode = data.measurement.measurementCode
                this.dataForm.measurementName = data.measurement.measurementName
                this.dataForm.note = data.measurement.note
                this.dataForm.deleted = data.measurement.deleted
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
              url: this.$http.adornUrl(`/car/measurement/${!this.dataForm.measurementId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'measurementId': this.dataForm.measurementId || undefined,
                'measurementCode': this.dataForm.measurementCode,
                'measurementName': this.dataForm.measurementName,
                'note': this.dataForm.note,
                'deleted': this.dataForm.deleted
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
