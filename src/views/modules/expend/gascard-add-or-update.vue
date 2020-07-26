<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="卡号" prop="cardNum">
      <el-input v-model="dataForm.cardNum" placeholder="卡号"></el-input>
    </el-form-item>
    <el-form-item label="站点" prop="site">
      <el-input v-model="dataForm.site" placeholder="站点"></el-input>
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
          gasCardId: 0,
          cardNum: '',
          site: '',
          note: ''
        },
        dataRule: {
          cardNum: [
            { required: true, message: '卡号不能为空', trigger: 'blur' }
          ],
          site: [
            { required: true, message: '站点不能为空', trigger: 'blur' }
          ],
          note: [
            { required: false, message: '备注不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.gasCardId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.gasCardId) {
            this.$http({
              url: this.$http.adornUrl(`/expend/gascard/info/${this.dataForm.gasCardId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.cardNum = data.gasCard.cardNum
                this.dataForm.site = data.gasCard.site
                this.dataForm.note = data.gasCard.note
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
              url: this.$http.adornUrl(`/expend/gascard/${!this.dataForm.gasCardId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'gasCardId': this.dataForm.gasCardId || undefined,
                'cardNum': this.dataForm.cardNum,
                'site': this.dataForm.site,
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
