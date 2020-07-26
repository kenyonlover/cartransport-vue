<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="车辆编码" prop="carCode">
      <el-input v-model="dataForm.carCode" placeholder="车辆编码" disabled></el-input>
    </el-form-item>
    <el-form-item label="车辆名称" prop="carName">
      <el-input v-model="dataForm.carName" placeholder="车辆名称"></el-input>
    </el-form-item>
    <el-form-item label="车牌号" prop="carNumber">
      <el-input v-model="dataForm.carNumber" placeholder="车牌号"></el-input>
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
          carInfoId: 0,
          carCode: '',
          carName: '',
          carNumber: '',
          note: '',
          deleted: ''
        },
        dataRule: {
          carCode: [
            { required: false, message: '车辆编码不能为空', trigger: 'blur' }
          ],
          carName: [
            { required: false, message: '车辆名称不能为空', trigger: 'blur' }
          ],
          carNumber: [
            { required: true, message: '车牌号不能为空', trigger: 'blur' }
          ],
          note: [
            { required: false, message: '备注不能为空', trigger: 'blur' }
          ],
          deleted: [
            { required: false, message: '删除标记不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.carInfoId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.carInfoId) {
            this.$http({
              url: this.$http.adornUrl(`/car/carinfo/info/${this.dataForm.carInfoId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.carCode = data.carInfo.carCode
                this.dataForm.carName = data.carInfo.carName
                this.dataForm.carNumber = data.carInfo.carNumber
                this.dataForm.note = data.carInfo.note
                this.dataForm.deleted = data.carInfo.deleted
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
              url: this.$http.adornUrl(`/car/carinfo/${!this.dataForm.carInfoId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'carInfoId': this.dataForm.carInfoId || undefined,
                'carCode': this.dataForm.carCode,
                'carName': this.dataForm.carName,
                'carNumber': this.dataForm.carNumber,
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
