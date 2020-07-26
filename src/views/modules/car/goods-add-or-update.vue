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
      <el-form-item label="货品编码" prop="goodsCode">
        <el-input v-model="dataForm.goodsCode" placeholder="货品编码" disabled></el-input>
      </el-form-item>
      <el-form-item label="货品名称" prop="goodsName">
        <el-input v-model="dataForm.goodsName" placeholder="货品名称"></el-input>
      </el-form-item>
      <!-- <el-form-item label="计量单位_ID" prop="measurementId">
      <el-input v-model="dataForm.measurementId" placeholder="计量单位_ID"></el-input>
      </el-form-item>-->
      <el-form-item label="计量单位" prop="measurementName">
        <el-input
          v-on:click.native="measurementClick"
          v-model="dataForm.measurementName"
          placeholder="计量单位"
        ></el-input>
      </el-form-item>
      <el-form-item label="备注" prop="note">
        <el-input v-model="dataForm.note" placeholder="备注"></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
    <measurement v-if="measurementVisible" ref="measurement" @func="getResultFromDialog"></measurement>
  </el-dialog>
</template>

<script>
import measurement from "./measurement-select-dialog";
export default {
  data() {
    return {
      visible: false,
      measurementVisible: false,
      dataForm: {
        goodsId: 0,
        goodsCode: "",
        goodsName: "",
        measurementId: "",
        measurementName: "",
        note: "",
        deleted: ""
      },
      dataRule: {
        goodsCode: [
          { required: false, message: "货品编码不能为空", trigger: "blur" }
        ],
        goodsName: [
          { required: true, message: "货品名称不能为空", trigger: "blur" }
        ],
        measurementId: [
          { required: false, message: "计量单位_ID不能为空", trigger: "blur" }
        ],
        measurementName: [
          { required: true, message: "计量单位不能为空", trigger: "blur" }
        ],
        note: [{ required: false, message: "备注不能为空", trigger: "blur" }]
      }
    };
  },
  components: {
    measurement
  },
  methods: {
    init(id) {
      this.dataForm.goodsId = id || 0;
      this.visible = true;
      this.$nextTick(() => {
        this.$refs["dataForm"].resetFields();
        if (this.dataForm.goodsId) {
          this.$http({
            url: this.$http.adornUrl(
              `/car/goods/info/${this.dataForm.goodsId}`
            ),
            method: "get",
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.goodsCode = data.goods.goodsCode;
              this.dataForm.goodsName = data.goods.goodsName;
              this.dataForm.measurementId = data.goods.measurementId;
              this.dataForm.measurementName = data.goods.measurementName;
              this.dataForm.note = data.goods.note;
              this.dataForm.deleted = data.goods.deleted;
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
              `/car/goods/${!this.dataForm.goodsId ? "save" : "update"}`
            ),
            method: "post",
            data: this.$http.adornData({
              goodsId: this.dataForm.goodsId || undefined,
              goodsCode: this.dataForm.goodsCode,
              goodsName: this.dataForm.goodsName,
              measurementId: this.dataForm.measurementId,
              measurementName: this.dataForm.measurementName,
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
    measurementClick() {
      this.measurementVisible = true;
      this.$nextTick(() => {
        this.$refs.measurement.init();
      });
    },
    getResultFromDialog(data) {
      this.dataForm.measurementId = data.measurementId;
      this.dataForm.measurementName = data.measurementName;
    }
  }
};
</script>
