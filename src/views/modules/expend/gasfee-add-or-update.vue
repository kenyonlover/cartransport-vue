<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible"
  >
    <el-form
      :model="dataForm"
      :rules="dataRule"
      ref="dataForm"
      @keyup.enter.native="dataFormSubmit()"
      label-width="80px"
    >
      <el-form-item label="编码" prop="gasFeeCode">
        <el-input v-model="dataForm.gasFeeCode" placeholder="编码" disabled></el-input>
      </el-form-item>
      <!-- <el-form-item label="车辆ID" prop="carId">
      <el-input v-model="dataForm.carId" placeholder="车辆ID"></el-input>
      </el-form-item>-->
      <el-form-item label="车辆" prop="carNum">
        <el-input v-on:click.native="carinfoClick" v-model="dataForm.carNum" placeholder="车辆"></el-input>
      </el-form-item>
      <el-form-item label="日期" prop="gasDate">
        <!-- <el-input v-model="dataForm.gasDate" placeholder="日期"></el-input> -->
        <el-date-picker
          v-model="dataForm.gasDate"
          type="date"
          placeholder="日期"
          value-format="yyyy-MM-dd"
          format="yyyy-MM-dd"
        ></el-date-picker>
      </el-form-item>
      <!-- <el-form-item label="加油卡ID" prop="gasCardId">
      <el-input v-model="dataForm.gasCardId" placeholder="加油卡ID"></el-input>
      </el-form-item>-->
      <el-form-item label="加油卡" prop="gasCardName">
        <el-input v-on:click.native="gascardClick" v-model="dataForm.gasCardName" placeholder="加油卡"></el-input>
      </el-form-item>
      <el-form-item label="升" prop="litre">
        <!-- <el-input v-model="dataForm.litre" placeholder="升"></el-input> -->
        <el-input-number
          :min="0"
          @change="calculateTotalMoney"
          v-model="dataForm.litre"
          :precision="2"
          :step="0.01"
        ></el-input-number>
      </el-form-item>
      <el-form-item label="单价" prop="price">
        <!-- <el-input v-model="dataForm.price" placeholder="单价"></el-input> -->
        <el-input-number
          :min="0"
          @change="calculateTotalMoney"
          v-model="dataForm.price"
          :precision="2"
          :step="0.01"
        ></el-input-number>
      </el-form-item>
      <el-form-item label="金额" prop="money">
        <!-- <el-input v-model="dataForm.money" placeholder="金额"></el-input> -->
        <el-input-number :min="0" v-model="dataForm.money" :precision="2" :step="0.01"></el-input-number>
      </el-form-item>
      <el-form-item label="备注" prop="note">
        <el-input v-model="dataForm.note" placeholder="备注"></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
    <carinfo v-if="carinfoVisible" ref="carinfo" @func="getCarinfoFromDialog"></carinfo>
    <gascard v-if="gascardVisible" ref="gascard" @func="getGascardFromDialog"></gascard>
  </el-dialog>
</template>

<script>
import carinfo from "../car/carinfo-select-dialog";
import gascard from "./gascard-select-dialog";
export default {
  data() {
    return {
      visible: false,
      carinfoVisible: false,
      gascardVisible: false,
      dataForm: {
        gasFeeId: 0,
        gasFeeCode: "",
        carId: "",
        carNum: "",
        gasDate: "",
        gasCardId: "",
        gasCardName: "",
        litre: "",
        price: "",
        money: "",
        note: "",
        deleted: ""
      },
      dataRule: {
        gasFeeCode: [
          { required: false, message: "编码不能为空", trigger: "blur" }
        ],
        carId: [{ required: true, message: "车辆ID不能为空", trigger: "blur" }],
        carNum: [{ required: true, message: "车辆不能为空", trigger: "blur" }],
        gasDate: [{ required: true, message: "日期不能为空", trigger: "blur" }],
        gasCardId: [
          { required: true, message: "加油卡ID不能为空", trigger: "blur" }
        ],
        gasCardName: [
          { required: true, message: "加油卡不能为空", trigger: "blur" }
        ],
        litre: [{ required: true, message: "升不能为空", trigger: "blur" }],
        price: [{ required: true, message: "单价不能为空", trigger: "blur" }],
        money: [{ required: true, message: "金额不能为空", trigger: "blur" }],
        note: [{ required: false, message: "备注不能为空", trigger: "blur" }],
        deleted: [
          { required: false, message: "删除标记不能为空", trigger: "blur" }
        ]
      }
    };
  },
  components: {
    carinfo,
    gascard
  },
  methods: {
    init(id) {
      this.dataForm.gasFeeId = id || 0;
      this.visible = true;
      this.$nextTick(() => {
        this.$refs["dataForm"].resetFields();
        if (this.dataForm.gasFeeId) {
          this.$http({
            url: this.$http.adornUrl(
              `/expend/gasfee/info/${this.dataForm.gasFeeId}`
            ),
            method: "get",
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.gasFeeCode = data.gasFee.gasFeeCode;
              this.dataForm.carId = data.gasFee.carId;
              this.dataForm.carNum = data.gasFee.carNum;
              this.dataForm.gasDate = data.gasFee.gasDate;
              this.dataForm.gasCardId = data.gasFee.gasCardId;
              this.dataForm.gasCardName = data.gasFee.gasCardName;
              this.dataForm.litre = data.gasFee.litre;
              this.dataForm.price = data.gasFee.price;
              this.dataForm.money = data.gasFee.money;
              this.dataForm.note = data.gasFee.note;
              this.dataForm.deleted = data.gasFee.deleted;
            }
          });
        }
      });
    },
    // 表单提交
    dataFormSubmit() {
      this.$refs["dataForm"].validate(valid => {
        if (valid) {
          this.$http({
            url: this.$http.adornUrl(
              `/expend/gasfee/${!this.dataForm.gasFeeId ? "save" : "update"}`
            ),
            method: "post",
            data: this.$http.adornData({
              gasFeeId: this.dataForm.gasFeeId || undefined,
              gasFeeCode: this.dataForm.gasFeeCode,
              carId: this.dataForm.carId,
              carNum: this.dataForm.carNum,
              gasDate: this.dataForm.gasDate,
              gasCardId: this.dataForm.gasCardId,
              gasCardName: this.dataForm.gasCardName,
              litre: this.dataForm.litre,
              price: this.dataForm.price,
              money: this.dataForm.money,
              note: this.dataForm.note,
              deleted: this.dataForm.deleted
            })
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.$message({
                message: "操作成功",
                type: "success",
                duration: 1500,
                onClose: () => {
                  this.visible = false;
                  this.$emit("refreshDataList");
                }
              });
            } else {
              this.$message.error(data.msg);
            }
          });
        }
      });
    },
    //选择车辆
    carinfoClick() {
      console.log("进入选择车辆");
      this.carinfoVisible = true;
      this.$nextTick(() => {
        this.$refs.carinfo.init();
      });
    },
    //车辆选择返回值
    getCarinfoFromDialog(data) {
      this.dataForm.carId = data.carInfoId;
      this.dataForm.carNum = data.carNumber;
    },
    //计算总价
    calculateTotalMoney() {
      if (this.dataForm.price > 0 && this.dataForm.litre > 0) {
        this.dataForm.money = this.dataForm.price * this.dataForm.litre;
      }
    },
    //选择加油卡
    gascardClick() {
      console.log("进入选择加油卡");
      this.gascardVisible = true;
      this.$nextTick(() => {
        this.$refs.gascard.init();
      });
    },
    //加油卡选择返回值
    getGascardFromDialog(data) {
      this.dataForm.gasCardId = data.gasCardId;
      this.dataForm.gasCardName = data.cardNum;
    }
  }
};
</script>
