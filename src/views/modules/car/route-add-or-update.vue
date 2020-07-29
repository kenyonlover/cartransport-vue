<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="路线编码" prop="routeCode">
      <el-input v-model="dataForm.routeCode" placeholder="路线编码" disabled="true"></el-input>
    </el-form-item>
    <el-form-item label="路线" prop="routeName">
      <el-input v-model="dataForm.routeName" placeholder="路线"></el-input>
    </el-form-item>
    <!-- <el-form-item label="货品_ID" prop="goodsId">
      <el-input v-model="dataForm.goodsId" placeholder="货品"></el-input>
    </el-form-item> -->
    <el-form-item label="货品" prop="goodsName">
      <el-input v-on:click.native="goodsClick" v-model="dataForm.goodsName" placeholder="货品"></el-input>
    </el-form-item>
    <el-form-item label="运费单价/元" prop="freightPrice">
      <el-input-number :min="0" v-model="dataForm.freightPrice" :precision="2" :step="0.01"></el-input-number>
    </el-form-item>
    <el-form-item label="工资拖车单价/元" prop="trailerPrice">
      <el-input-number :min="0" v-model="dataForm.trailerPrice" :precision="2" :step="0.01"></el-input-number>
    </el-form-item>
    <el-form-item label="工资四桥车单价/元" prop="fourAxlePrice">
      <el-input-number :min="0" v-model="dataForm.fourAxlePrice" :precision="2" :step="0.01"></el-input-number>
    </el-form-item>
    <el-form-item label="工资提成比例(%)" prop="percentage">
      <el-input-number :min="0" v-model="dataForm.percentage" :precision="2" :step="0.01"></el-input-number>
    </el-form-item>
    <el-form-item label="备注" prop="note">
      <el-input v-model="dataForm.note" placeholder="备注"></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
    <goods v-if="goodsVisible" ref="goods" @func="getGoodsFromDialog"></goods>
  </el-dialog>
</template>

<script>
  import goods from "./goods-select-dialog";
  export default {
    data () {
      return {
        visible: false,
        goodsVisible: false,
        dataForm: {
          routeId: 0,
          routeCode: '',
          routeName: '',
          goodsId: '',
          goodsName: '',
          trailerPrice: '',
          freightPrice: '',
          fourAxlePrice: '',
          percentage: '',
          note: '',
          deleted: ''
        },
        dataRule: {
          routeCode: [
            { required: false, message: '路线编码不能为空', trigger: 'blur' }
          ],
          routeName: [
            { required: true, message: '路线不能为空', trigger: 'blur' }
          ],
          goodsId: [
            { required: false, message: '货品_ID不能为空', trigger: 'blur' }
          ],
          goodsName: [
            { required: true, message: '货品不能为空', trigger: 'blur' }
          ],
          freightPrice: [
            { required: false, message: '运费单价/元不能为空', trigger: 'blur' }
          ],
          trailerPrice: [
            { required: false, message: '拖车单价/元不能为空', trigger: 'blur' }
          ],
          fourAxlePrice: [
            { required: false, message: '四桥车单价/元不能为空', trigger: 'blur' }
          ],
          percentage: [
            { required: false, message: '提成比例(%)不能为空', trigger: 'blur' }
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
    components: {
    goods
  },
    methods: {
      init (id) {
        this.dataForm.routeId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.routeId) {
            this.$http({
              url: this.$http.adornUrl(`/car/route/info/${this.dataForm.routeId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.routeCode = data.route.routeCode
                this.dataForm.routeName = data.route.routeName
                this.dataForm.goodsId = data.route.goodsId
                this.dataForm.goodsName = data.route.goodsName
                this.dataForm.freightPrice = data.route.freightPrice
                this.dataForm.trailerPrice = data.route.trailerPrice
                this.dataForm.fourAxlePrice = data.route.fourAxlePrice
                this.dataForm.percentage = data.route.percentage
                this.dataForm.note = data.route.note
                this.dataForm.deleted = data.route.deleted
              } else {
                this.$message.error(data.msg);
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            //进行单价填写校验，拖车单价与四桥车单价必须同时填写，拖车单价、四桥车单价与提成比例互斥
            if((this.dataForm.trailerPrice && !this.dataForm.fourAxlePrice) || (!this.dataForm.trailerPrice && this.dataForm.fourAxlePrice)){
              this.$message.error('拖车单价与四桥车单价必须同时填写');
              return
            }
            if((this.dataForm.trailerPrice && this.dataForm.percentage) || (this.dataForm.fourAxlePrice && this.dataForm.percentage)){
              this.$message.error('拖车单价、四桥车单价与提成比例互斥');
              return
            }
            if(!this.dataForm.trailerPrice && !this.dataForm.fourAxlePrice && !this.dataForm.percentage){
              this.$message.error('拖车单价、四桥车单价与提成比例不能同时为空');
              return
            }

            this.$http({
              url: this.$http.adornUrl(`/car/route/${!this.dataForm.routeId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'routeId': this.dataForm.routeId || undefined,
                'routeCode': this.dataForm.routeCode,
                'routeName': this.dataForm.routeName,
                'goodsId': this.dataForm.goodsId,
                'goodsName': this.dataForm.goodsName,
                'freightPrice': this.dataForm.freightPrice,
                'trailerPrice': this.dataForm.trailerPrice,
                'fourAxlePrice': this.dataForm.fourAxlePrice,
                'percentage': this.dataForm.percentage,
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
      },
      //选择货品
      goodsClick() {
        this.goodsVisible = true;
        this.$nextTick(() => {
          this.$refs.goods.init();
        });
      },
      //货品选择返回值
      getGoodsFromDialog(data) {
        this.dataForm.goodsId = data.goodsId;
        this.dataForm.goodsName = data.goodsName;
      }
    }
  }
</script>
