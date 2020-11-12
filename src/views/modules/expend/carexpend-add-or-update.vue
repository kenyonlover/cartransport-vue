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
      <el-form-item label="编码" prop="carExpendCode">
        <el-input v-model="dataForm.carExpendCode" placeholder="编码" disabled="true"></el-input>
      </el-form-item>
      <!-- <el-form-item label="车辆ID" prop="carId">
      <el-input v-model="dataForm.carId" placeholder="车辆ID"></el-input>
      </el-form-item>-->
      <el-form-item label="车辆" prop="carNum">
        <el-input v-on:click.native="carinfoClick" v-model="dataForm.carNum" placeholder="车辆"></el-input>
      </el-form-item>
      <!-- <el-form-item label="支出类型ID" prop="expendTypeId">
      <el-input v-model="dataForm.expendTypeId" placeholder="支出类型ID"></el-input>
      </el-form-item>-->
      <el-form-item label="支出类型" prop="expendTypeName">
        <el-input
          v-on:click.native="expendTypeClick"
          v-model="dataForm.expendTypeName"
          placeholder="支出类型"
        ></el-input>
      </el-form-item>
      <el-form-item label="明细" prop="detailed">
        <el-input v-model="dataForm.detailed" placeholder="明细"></el-input>
      </el-form-item>
      <el-form-item label="日期" prop="expendDate">
        <!-- <el-input v-model="dataForm.expendDate" placeholder="日期"></el-input> -->
        <el-date-picker
          v-model="dataForm.expendDate"
          type="date"
          placeholder="日期"
          value-format="yyyy-MM-dd"
          format="yyyy-MM-dd"
        ></el-date-picker>
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
    <expendType v-if="expendTypeVisible" ref="expendType" @func="getExpendTypeFromDialog"></expendType>
  </el-dialog>
</template>

<script>
import carinfo from "../car/carinfo-select-dialog";
import expendType from "./expendtype-select-dialog";
export default {
  data() {
    return {
      visible: false,
      carinfoVisible: false,
      expendTypeVisible: false,
      dataForm: {
        carExpendId: 0,
        carExpendCode: "",
        carId: "",
        carNum: "",
        expendTypeId: "",
        expendTypeName: "",
        detailed: "",
        expendDate: "",
        money: "",
        note: "",
        deleted: ""
      },
      dataRule: {
        carExpendCode: [
          { required: false, message: "编码不能为空", trigger: "blur" }
        ],
        carId: [{ required: true, message: "车辆ID不能为空", trigger: "blur" }],
        carNum: [{ required: true, message: "车辆不能为空", trigger: "change" }],
        expendTypeId: [
          { required: true, message: "支出类型ID不能为空", trigger: "blur" }
        ],
        expendTypeName: [
          { required: true, message: "支出类型不能为空", trigger: "change" }
        ],
        detailed: [
          { required: false, message: "明细不能为空", trigger: "blur" }
        ],
        expendDate: [
          { required: true, message: "日期不能为空", trigger: "blur" }
        ],
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
    expendType
  },
  methods: {
    init(id) {
      this.dataForm.carExpendId = id || 0;
      this.visible = true;
      this.$nextTick(() => {
        this.$refs["dataForm"].resetFields();
        if (this.dataForm.carExpendId) {
          this.$http({
            url: this.$http.adornUrl(
              `/expend/carexpend/info/${this.dataForm.carExpendId}`
            ),
            method: "get",
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.carExpendCode = data.carExpend.carExpendCode;
              this.dataForm.carId = data.carExpend.carId;
              this.dataForm.carNum = data.carExpend.carNum;
              this.dataForm.expendTypeId = data.carExpend.expendTypeId;
              this.dataForm.expendTypeName = data.carExpend.expendTypeName;
              this.dataForm.detailed = data.carExpend.detailed;
              this.dataForm.expendDate = data.carExpend.expendDate;
              this.dataForm.money = data.carExpend.money;
              this.dataForm.note = data.carExpend.note;
              this.dataForm.deleted = data.carExpend.deleted;
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
              `/expend/carexpend/${
                !this.dataForm.carExpendId ? "save" : "update"
              }`
            ),
            method: "post",
            data: this.$http.adornData({
              carExpendId: this.dataForm.carExpendId || undefined,
              carExpendCode: this.dataForm.carExpendCode,
              carId: this.dataForm.carId,
              carNum: this.dataForm.carNum,
              expendTypeId: this.dataForm.expendTypeId,
              expendTypeName: this.dataForm.expendTypeName,
              detailed: this.dataForm.detailed,
              expendDate: this.dataForm.expendDate,
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
    //选择支出类型
    expendTypeClick() {
      console.log("进入选择车辆");
      this.expendTypeVisible = true;
      this.$nextTick(() => {
        this.$refs.expendType.init();
      });
    },
    //加油卡选择返回值
    getExpendTypeFromDialog(data) {
      this.dataForm.expendTypeId = data.expendTypeId;
      this.dataForm.expendTypeName = data.name;
    }
  }
};
</script>
