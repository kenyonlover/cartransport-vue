<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="客商编码" prop="traderCode">
      <el-input v-model="dataForm.traderCode" placeholder="客商编码" disabled></el-input>
    </el-form-item>
    <el-form-item label="客商名称" prop="traderName">
      <el-input v-model="dataForm.traderName" placeholder="客商名称"></el-input>
    </el-form-item>
    <el-form-item label="发货方" prop="isShipper">
      <el-radio-group v-model="dataForm.isShipper">
        <el-radio :label="1">是</el-radio>
        <el-radio :label="0">否</el-radio>
      </el-radio-group>
    </el-form-item>
    <el-form-item label="收货方" prop="isReceiver">
      <el-radio-group v-model="dataForm.isReceiver">
        <el-radio :label="1">是</el-radio>
        <el-radio :label="0">否</el-radio>
      </el-radio-group>
    </el-form-item>
    <el-form-item label="承运单位" prop="isCarrier">
      <el-radio-group v-model="dataForm.isCarrier">
        <el-radio :label="1">是</el-radio>
        <el-radio :label="0">否</el-radio>
      </el-radio-group>
    </el-form-item>
    <el-form-item label="联系人" prop="linkman">
      <el-input v-model="dataForm.linkman" placeholder="联系人"></el-input>
    </el-form-item>
    <el-form-item label="联系方式" prop="linkInformation">
      <el-input v-model="dataForm.linkInformation" placeholder="联系方式"></el-input>
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
          travellingTraderId: 0,
          traderCode: '',
          traderName: '',
          isShipper: 0,
          isReceiver: 0,
          isCarrier: 0,
          linkman: '',
          linkInformation: '',
          note: '',
          deleted: ''
        },
        dataRule: {
          traderCode: [
            { required: false, message: '客商编码不能为空', trigger: 'blur' }
          ],
          traderName: [
            { required: true, message: '客商名称不能为空', trigger: 'blur' }
          ],
          isShipper: [
            { required: false, message: '发货方不能为空', trigger: 'blur' }
          ],
          isReceiver: [
            { required: false, message: '收货方不能为空', trigger: 'blur' }
          ],
          isCarrier: [
            { required: false, message: '承运单位不能为空', trigger: 'blur' }
          ],
          linkman: [
            { required: false, message: '联系人不能为空', trigger: 'blur' }
          ],
          linkInformation: [
            { required: false, message: '联系方式不能为空', trigger: 'blur' }
          ],
          note: [
            { required: false, message: '备注不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.travellingTraderId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.travellingTraderId) {
            this.$http({
              url: this.$http.adornUrl(`/car/travellingtrader/info/${this.dataForm.travellingTraderId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.traderCode = data.travellingTrader.traderCode
                this.dataForm.traderName = data.travellingTrader.traderName
                this.dataForm.isShipper = data.travellingTrader.isShipper
                this.dataForm.isReceiver = data.travellingTrader.isReceiver
                this.dataForm.isCarrier = data.travellingTrader.isCarrier
                this.dataForm.linkman = data.travellingTrader.linkman
                this.dataForm.linkInformation = data.travellingTrader.linkInformation
                this.dataForm.note = data.travellingTrader.note
                this.dataForm.deleted = data.travellingTrader.deleted
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
              url: this.$http.adornUrl(`/car/travellingtrader/${!this.dataForm.travellingTraderId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'travellingTraderId': this.dataForm.travellingTraderId || undefined,
                'traderCode': this.dataForm.traderCode,
                'traderName': this.dataForm.traderName,
                'isShipper': this.dataForm.isShipper,
                'isReceiver': this.dataForm.isReceiver,
                'isCarrier': this.dataForm.isCarrier,
                'linkman': this.dataForm.linkman,
                'linkInformation': this.dataForm.linkInformation,
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
