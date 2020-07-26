<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible"
    width="80%"
  >
    <el-form
      :model="dataForm"
      :rules="dataRule"
      ref="dataForm"
      @keyup.enter.native="dataFormSubmit()"
      label-width="80px"
    >
      <el-col :span="8">
        <el-form-item label="运输编码" prop="carTravelCode" label-width="30%">
          <el-input v-model="dataForm.carTravelCode" placeholder="运输编码" disabled></el-input>
        </el-form-item>
      </el-col>
      <!-- <el-col :span="8">
        <el-form-item label="发货方_ID" prop="shipperId" label-width="30%">
          <el-input v-model="dataForm.shipperId" placeholder="发货方_ID"></el-input>
        </el-form-item>
      </el-col>-->
      <el-col :span="8">
        <el-form-item label="发货方" prop="shipperName" label-width="30%">
          <el-input
            v-on:click.native="shipperClick"
            v-model="dataForm.shipperName"
            placeholder="发货方"
          ></el-input>
        </el-form-item>
      </el-col>
      <!-- <el-col :span="8">
        <el-form-item label="收货方_ID" prop="receiverId" label-width="30%">
          <el-input v-model="dataForm.receiverId" placeholder="收货方_ID"></el-input>
        </el-form-item>
      </el-col>-->
      <el-col :span="8">
        <el-form-item label="收货方" prop="receiverName" label-width="30%">
          <el-input
            v-on:click.native="receiverClick"
            v-model="dataForm.receiverName"
            placeholder="收货方"
          ></el-input>
        </el-form-item>
      </el-col>
      <!-- <el-col :span="8">
        <el-form-item label="承运单位_ID" prop="carrierId" label-width="30%">
          <el-input v-model="dataForm.carrierId" placeholder="承运单位_ID"></el-input>
        </el-form-item>
      </el-col>-->
      <el-col :span="8">
        <el-form-item label="承运单位" prop="carrierName" label-width="30%">
          <el-input
            v-on:click.native="carrierClick"
            v-model="dataForm.carrierName"
            placeholder="承运单位"
          ></el-input>
        </el-form-item>
      </el-col>
      <!-- <el-col :span="8">
        <el-form-item label="货品_ID" prop="goodsId" label-width="30%">
          <el-input v-model="dataForm.goodsId" placeholder="货品_ID"></el-input>
        </el-form-item>
      </el-col>-->
      <el-col :span="8">
        <el-form-item label="货品" prop="goodsName" label-width="30%">
          <el-input v-on:click.native="goodsClick" v-model="dataForm.goodsName" placeholder="货品"></el-input>
        </el-form-item>
      </el-col>
      <!-- <el-col :span="8">
        <el-form-item label="车牌_ID" prop="carInfoId" label-width="30%">
          <el-input v-model="dataForm.carInfoId" placeholder="车牌_ID"></el-input>
        </el-form-item>
      </el-col>-->
      <el-col :span="8">
        <el-form-item label="车牌" prop="carInfoName" label-width="30%">
          <el-input
            v-on:click.native="carinfoClick"
            v-model="dataForm.carInfoName"
            placeholder="车牌"
          ></el-input>
        </el-form-item>
      </el-col>
      <!-- <el-col :span="8">
        <el-form-item label="驾驶员_ID" prop="driverId" label-width="30%">
          <el-input v-model="dataForm.driverId" placeholder="驾驶员_ID"></el-input>
        </el-form-item>
      </el-col>-->
      <el-col :span="8">
        <el-form-item label="驾驶员" prop="driverName" label-width="30%">
          <el-input v-on:click.native="driverClick" v-model="dataForm.driverName" placeholder="驾驶员"></el-input>
        </el-form-item>
      </el-col>
      <el-col :span="8">
        <el-form-item label="收货日期" prop="receiveDate" label-width="30%">
          <!-- <el-input v-model="dataForm.receiveDate" placeholder="收货日期"></el-input> -->
          <el-date-picker v-model="dataForm.receiveDate" type="date" placeholder="收货日期" value-format="yyyy-MM-dd" format="yyyy-MM-dd"></el-date-picker>
        </el-form-item>
      </el-col>
      <el-col :span="8">
        <el-form-item label="毛重" @change="weightChange" prop="grossWeight" label-width="30%">
          <!-- <el-input v-model="dataForm.grossWeight" placeholder="毛重"></el-input> -->
          <el-input-number v-model="dataForm.grossWeight" :precision="2" :step="0.01"></el-input-number>
        </el-form-item>
      </el-col>
      <el-col :span="8">
        <el-form-item label="皮重" @change="weightChange" prop="tareWeight" label-width="30%">
          <!-- <el-input v-model="dataForm.tareWeight" placeholder="皮重"></el-input> -->
          <el-input-number v-model="dataForm.tareWeight" :precision="2" :step="0.01"></el-input-number>
        </el-form-item>
      </el-col>
      <el-col :span="8">
        <el-form-item label="净重" prop="netWeight" label-width="30%">
          <!-- <el-input v-model="dataForm.netWeight" placeholder="净重"></el-input> -->
          <el-input-number v-model="dataForm.netWeight" :precision="2" :step="0.01"></el-input-number>
        </el-form-item>
      </el-col>
      <!-- <el-col :span="8">
        <el-form-item label="计量单位_ID" prop="measurementId" label-width="30%">
          <el-input v-model="dataForm.measurementId" placeholder="计量单位_ID"></el-input>
        </el-form-item>
      </el-col>-->
      <el-col :span="8">
        <el-form-item label="计量单位" prop="measurementName" label-width="30%">
          <el-input
            v-on:click.native="measurementClick"
            v-model="dataForm.measurementName"
            placeholder="计量单位"
          ></el-input>
        </el-form-item>
      </el-col>
      <el-col :span="8">
        <el-form-item label="发货方日期" prop="deliveryDate" label-width="30%">
          <!-- <el-input v-model="dataForm.deliveryDate" placeholder="发货方日期"></el-input> -->
          <el-date-picker v-model="dataForm.deliveryDate" type="date" placeholder="发货方日期" value-format="yyyy-MM-dd" format="yyyy-MM-dd"></el-date-picker>
        </el-form-item>
      </el-col>
      <el-col :span="8">
        <el-form-item label="发货方净重" prop="deliveryNetWeight" label-width="30%">
          <!-- <el-input v-model="dataForm.deliveryNetWeight" placeholder="发货方净重"></el-input> -->
          <el-input-number v-model="dataForm.deliveryNetWeight" :precision="2" :step="0.01"></el-input-number>
        </el-form-item>
      </el-col>
      <!-- <el-col :span="8">
        <el-form-item label="路线" prop="loadingPoint" label-width="30%">
          <el-input v-model="dataForm.loadingPoint" placeholder="路线"></el-input>
        </el-form-item>
      </el-col> -->
      <el-col :span="8">
        <el-form-item label="路线" prop="loadingPointName" label-width="30%">
          <el-input v-on:click.native="loadingPointClick(dataForm.goodsId)" v-model="dataForm.loadingPointName" placeholder="路线"></el-input>
        </el-form-item>
      </el-col>
      <!-- <el-col :span="8">
        <el-form-item label="卸货点" prop="dischargePoint" label-width="30%">
          <el-input v-model="dataForm.dischargePoint" placeholder="卸货点"></el-input>
        </el-form-item>
      </el-col> -->
      <el-col :span="8">
        <el-form-item label="装车费/元" prop="loadingFee" label-width="30%">
          <!-- <el-input v-model="dataForm.loadingFee" placeholder="装车费"></el-input> -->
          <el-input-number v-model="dataForm.loadingFee" :precision="2" :step="0.01"></el-input-number>
        </el-form-item>
      </el-col>
      <el-col :span="8">
        <el-form-item label="过磅费/元" prop="weighingFee" label-width="30%">
          <!-- <el-input v-model="dataForm.weighingFee" placeholder="过磅费"></el-input> -->
          <el-input-number v-model="dataForm.weighingFee" :precision="2" :step="0.01"></el-input-number>
        </el-form-item>
      </el-col>
      <el-col :span="8">
        <el-form-item label="是否入库" prop="isStorage" label-width="30%">
          <!-- <el-input v-model="dataForm.isStorage" placeholder="是否入库"></el-input> -->
          <el-radio-group v-model="dataForm.isStorage">
            <el-radio :label="1">是</el-radio>
            <el-radio :label="0">否</el-radio>
          </el-radio-group>
        </el-form-item>
      </el-col>
      <el-col :span="8">
        <el-form-item label="是否出库" prop="isOutbound" label-width="30%">
          <!-- <el-input v-model="dataForm.isOutbound" placeholder="是否出库"></el-input> -->
          <el-radio-group v-model="dataForm.isOutbound">
            <el-radio :label="1">是</el-radio>
            <el-radio :label="0">否</el-radio>
          </el-radio-group>
        </el-form-item>
      </el-col>
      <el-col :span="8">
        <el-form-item label="是否核对" prop="isCheck" label-width="30%">
          <!-- <el-input v-model="dataForm.isCheck" placeholder="是否核对"></el-input> -->
          <el-radio-group v-model="dataForm.isCheck">
            <el-radio :label="1" disabled>是</el-radio>
            <el-radio :label="0" disabled>否</el-radio>
          </el-radio-group>
        </el-form-item>
      </el-col>
      <el-col :span="8">
        <el-form-item label="备注" prop="note" label-width="30%">
          <el-input v-model="dataForm.note" placeholder="备注"></el-input>
        </el-form-item>
      </el-col>
      <!-- <el-col :span="8">
        <el-form-item label="创建人_ID" prop="creater" label-width="30%">
          <el-input v-model="dataForm.creater" placeholder="创建人_ID"></el-input>
        </el-form-item>
      </el-col>
      <el-col :span="8">
        <el-form-item label="创建人" prop="createrName" label-width="30%">
          <el-input v-model="dataForm.createrName" placeholder="创建人"></el-input>
        </el-form-item>
      </el-col>
      <el-col :span="8">
        <el-form-item label="创建时间" prop="createtime" label-width="30%">
          <el-input v-model="dataForm.createtime" placeholder="创建时间"></el-input>
        </el-form-item>
      </el-col>
      <el-col :span="8">
        <el-form-item label="核对人_ID" prop="checker" label-width="30%">
          <el-input v-model="dataForm.checker" placeholder="核对人_ID"></el-input>
        </el-form-item>
      </el-col>
      <el-col :span="8">
        <el-form-item label="核对人" prop="checkerName" label-width="30%">
          <el-input v-model="dataForm.checkerName" placeholder="核对人"></el-input>
        </el-form-item>
      </el-col>
      <el-col :span="8">
        <el-form-item label="核对时间" prop="checktime" label-width="30%">
          <el-input v-model="dataForm.checktime" placeholder="核对时间"></el-input>
        </el-form-item>
      </el-col>
      <el-col :span="8">
        <el-form-item label="修改人_ID" prop="reviser" label-width="30%">
          <el-input v-model="dataForm.reviser" placeholder="修改人_ID"></el-input>
        </el-form-item>
      </el-col>
      <el-col :span="8">
        <el-form-item label="修改人" prop="reviserName" label-width="30%">
          <el-input v-model="dataForm.reviserName" placeholder="修改人"></el-input>
        </el-form-item>
      </el-col>
      <el-col :span="8">
        <el-form-item label="修改时间" prop="revisetime" label-width="30%">
          <el-input v-model="dataForm.revisetime" placeholder="修改时间"></el-input>
        </el-form-item>
      </el-col>
      <el-col :span="8">
        <el-form-item label="删除标记" prop="deleted" label-width="30%">
          <el-input v-model="dataForm.deleted" placeholder="删除标记"></el-input>
        </el-form-item>
      </el-col>-->
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
    <travellingtrader
      v-if="travellingtraderVisible"
      ref="travellingtrader"
      @func="getTraderFromDialog"
    ></travellingtrader>
    <goods v-if="goodsVisible" ref="goods" @func="getGoodsFromDialog"></goods>
    <carinfo v-if="carinfoVisible" ref="carinfo" @func="getCarinfoFromDialog"></carinfo>
    <driver v-if="driverVisible" ref="driver" @func="getDriverFromDialog"></driver>
    <measurement v-if="measurementVisible" ref="measurement" @func="getMeasurementFromDialog"></measurement>
    <route v-if="routeVisible" ref="route" @func="getRouteFromDialog"></route>
  </el-dialog>
</template>

<script>
import travellingtrader from "./travellingtrader-select-dialog";
import goods from "./goods-select-dialog";
import carinfo from "./carinfo-select-dialog";
import driver from "./driver-select-dialog";
import measurement from "./measurement-select-dialog";
import route from "./route-select-dialog";
export default {
  data() {
    return {
      visible: false,
      travellingtraderVisible: false,
      goodsVisible: false,
      carinfoVisible: false,
      driverVisible: false,
      measurementVisible: false,
      routeVisible: false,
      dataForm: {
        carTravelId: 0,
        carTravelCode: "",
        shipperId: "",
        shipperName: "",
        receiverId: "",
        receiverName: "",
        carrierId: "",
        carrierName: "",
        goodsId: "",
        goodsName: "",
        carInfoId: "",
        carInfoName: "",
        driverId: "",
        driverName: "",
        receiveDate: "",
        grossWeight: 0.0,
        tareWeight: 0.0,
        netWeight: 0.0,
        measurementId: "",
        measurementName: "",
        deliveryDate: "",
        deliveryNetWeight: 0.0,
        loadingPoint: "",
        loadingPointName: "",
        dischargePoint: "",
        loadingFee: 0.0,
        weighingFee: 0.0,
        isStorage: 0,
        isOutbound: 0,
        note: "",
        isCheck: 0,
        creater: "",
        createrName: "",
        createtime: "",
        reviser: "",
        reviserName: "",
        revisetime: "",
        checker: "",
        checkerName: "",
        checktime: "",
        deleted: ""
      },
      dataRule: {
        carTravelCode: [
          { required: false, message: "运输编码不能为空", trigger: "blur" }
        ],
        shipperId: [
          { required: true, message: "发货方_ID不能为空", trigger: "blur" }
        ],
        shipperName: [
          { required: true, message: "发货方不能为空", trigger: "blur" }
        ],
        receiverId: [
          { required: true, message: "收货方_ID不能为空", trigger: "blur" }
        ],
        receiverName: [
          { required: true, message: "收货方不能为空", trigger: "blur" }
        ],
        carrierId: [
          { required: true, message: "承运单位_ID不能为空", trigger: "blur" }
        ],
        carrierName: [
          { required: true, message: "承运单位不能为空", trigger: "blur" }
        ],
        goodsId: [
          { required: true, message: "货品_ID不能为空", trigger: "blur" }
        ],
        goodsName: [
          { required: true, message: "货品不能为空", trigger: "blur" }
        ],
        carInfoId: [
          { required: true, message: "车牌_ID不能为空", trigger: "blur" }
        ],
        carInfoName: [
          { required: true, message: "车牌不能为空", trigger: "blur" }
        ],
        driverId: [
          { required: true, message: "驾驶员_ID不能为空", trigger: "blur" }
        ],
        driverName: [
          { required: true, message: "驾驶员不能为空", trigger: "blur" }
        ],
        receiveDate: [
          { required: true, message: "收货日期不能为空", trigger: "blur" }
        ],
        grossWeight: [
          { required: true, message: "毛重不能为空", trigger: "blur" }
        ],
        tareWeight: [
          { required: true, message: "皮重不能为空", trigger: "blur" }
        ],
        netWeight: [
          { required: true, message: "净重不能为空", trigger: "blur" }
        ],
        measurementId: [
          { required: true, message: "计量单位_ID不能为空", trigger: "blur" }
        ],
        measurementName: [
          { required: true, message: "计量单位不能为空", trigger: "blur" }
        ],
        deliveryDate: [
          { required: true, message: "发货方日期不能为空", trigger: "blur" }
        ],
        deliveryNetWeight: [
          { required: true, message: "发货方净重不能为空", trigger: "blur" }
        ],
        loadingPoint: [
          { required: true, message: "路线不能为空", trigger: "blur" }
        ],
        loadingPointName: [
          { required: true, message: "路线不能为空", trigger: "blur" }
        ],
        dischargePoint: [
          { required: false, message: "卸货点不能为空", trigger: "blur" }
        ],
        loadingFee: [
          { required: true, message: "装车费不能为空", trigger: "blur" }
        ],
        weighingFee: [
          { required: true, message: "过磅费不能为空", trigger: "blur" }
        ],
        isStorage: [
          { required: true, message: "是否入库不能为空", trigger: "blur" }
        ],
        isOutbound: [
          { required: true, message: "是否出库不能为空", trigger: "blur" }
        ],
        note: [{ required: false, message: "备注不能为空", trigger: "blur" }],
        isCheck: [
          { required: true, message: "是否核对不能为空", trigger: "blur" }
        ],
        creater: [
          { required: false, message: "创建人_ID不能为空", trigger: "blur" }
        ],
        createrName: [
          { required: false, message: "创建人不能为空", trigger: "blur" }
        ],
        createtime: [
          { required: false, message: "创建时间不能为空", trigger: "blur" }
        ],
        reviser: [
          { required: false, message: "修改人_ID不能为空", trigger: "blur" }
        ],
        reviserName: [
          { required: false, message: "修改人不能为空", trigger: "blur" }
        ],
        revisetime: [
          { required: false, message: "修改时间不能为空", trigger: "blur" }
        ],
        checker: [
          { required: false, message: "核对人_ID不能为空", trigger: "blur" }
        ],
        checkerName: [
          { required: false, message: "核对人不能为空", trigger: "blur" }
        ],
        checktime: [
          { required: false, message: "核对时间不能为空", trigger: "blur" }
        ],
        deleted: [
          { required: false, message: "删除标记不能为空", trigger: "blur" }
        ]
      }
    };
  },
  components: {
    travellingtrader,
    goods,
    carinfo,
    driver,
    measurement,
    route
  },
  methods: {
    init(id) {
      this.dataForm.carTravelId = id || 0;
      this.visible = true;
      this.$nextTick(() => {
        this.$refs["dataForm"].resetFields();
        if (this.dataForm.carTravelId) {
          this.$http({
            url: this.$http.adornUrl(
              `/car/cartravel/copyInfo/${this.dataForm.carTravelId}`
            ),
            method: "get",
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
                this.dataForm.carTravelId = 0;
              this.dataForm.carTravelCode = data.carTravel.carTravelCode;
              this.dataForm.shipperId = data.carTravel.shipperId;
              this.dataForm.shipperName = data.carTravel.shipperName;
              this.dataForm.receiverId = data.carTravel.receiverId;
              this.dataForm.receiverName = data.carTravel.receiverName;
              this.dataForm.carrierId = data.carTravel.carrierId;
              this.dataForm.carrierName = data.carTravel.carrierName;
              this.dataForm.goodsId = data.carTravel.goodsId;
              this.dataForm.goodsName = data.carTravel.goodsName;
              this.dataForm.carInfoId = data.carTravel.carInfoId;
              this.dataForm.carInfoName = data.carTravel.carInfoName;
              this.dataForm.driverId = data.carTravel.driverId;
              this.dataForm.driverName = data.carTravel.driverName;
              this.dataForm.receiveDate = data.carTravel.receiveDate;
              this.dataForm.grossWeight = data.carTravel.grossWeight;
              this.dataForm.tareWeight = data.carTravel.tareWeight;
              this.dataForm.netWeight = data.carTravel.netWeight;
              this.dataForm.measurementId = data.carTravel.measurementId;
              this.dataForm.measurementName = data.carTravel.measurementName;
              this.dataForm.deliveryDate = data.carTravel.deliveryDate;
              this.dataForm.deliveryNetWeight =
                data.carTravel.deliveryNetWeight;
              this.dataForm.loadingPoint = data.carTravel.loadingPoint;
              this.dataForm.loadingPointName = data.carTravel.loadingPointName;
              this.dataForm.dischargePoint = data.carTravel.dischargePoint;
              this.dataForm.loadingFee = data.carTravel.loadingFee;
              this.dataForm.weighingFee = data.carTravel.weighingFee;
              this.dataForm.isStorage = data.carTravel.isStorage;
              this.dataForm.isOutbound = data.carTravel.isOutbound;
              this.dataForm.note = data.carTravel.note;
              this.dataForm.isCheck = data.carTravel.isCheck;
              this.dataForm.creater = data.carTravel.creater;
              this.dataForm.createrName = data.carTravel.createrName;
              this.dataForm.createtime = data.carTravel.createtime;
              this.dataForm.reviser = data.carTravel.reviser;
              this.dataForm.reviserName = data.carTravel.reviserName;
              this.dataForm.revisetime = data.carTravel.revisetime;
              this.dataForm.checker = data.carTravel.checker;
              this.dataForm.checkerName = data.carTravel.checkerName;
              this.dataForm.checktime = data.carTravel.checktime;
              this.dataForm.deleted = data.carTravel.deleted;
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
              `/car/cartravel/${!this.dataForm.carTravelId ? "save" : "update"}`
            ),
            method: "post",
            data: this.$http.adornData({
              carTravelId: this.dataForm.carTravelId || undefined,
              carTravelCode: this.dataForm.carTravelCode,
              shipperId: this.dataForm.shipperId,
              shipperName: this.dataForm.shipperName,
              receiverId: this.dataForm.receiverId,
              receiverName: this.dataForm.receiverName,
              carrierId: this.dataForm.carrierId,
              carrierName: this.dataForm.carrierName,
              goodsId: this.dataForm.goodsId,
              goodsName: this.dataForm.goodsName,
              carInfoId: this.dataForm.carInfoId,
              carInfoName: this.dataForm.carInfoName,
              driverId: this.dataForm.driverId,
              driverName: this.dataForm.driverName,
              receiveDate: this.dataForm.receiveDate,
              grossWeight: this.dataForm.grossWeight,
              tareWeight: this.dataForm.tareWeight,
              netWeight: this.dataForm.netWeight,
              measurementId: this.dataForm.measurementId,
              measurementName: this.dataForm.measurementName,
              deliveryDate: this.dataForm.deliveryDate,
              deliveryNetWeight: this.dataForm.deliveryNetWeight,
              loadingPoint: this.dataForm.loadingPoint,
              loadingPointName: this.dataForm.loadingPointName,
              dischargePoint: this.dataForm.dischargePoint,
              loadingFee: this.dataForm.loadingFee,
              weighingFee: this.dataForm.weighingFee,
              isStorage: this.dataForm.isStorage,
              isOutbound: this.dataForm.isOutbound,
              note: this.dataForm.note,
              isCheck: this.dataForm.isCheck,
              creater: this.dataForm.creater,
              createrName: this.dataForm.createrName,
              createtime: this.dataForm.createtime,
              reviser: this.dataForm.reviser,
              reviserName: this.dataForm.reviserName,
              revisetime: this.dataForm.revisetime,
              checker: this.dataForm.checker,
              checkerName: this.dataForm.checkerName,
              checktime: this.dataForm.checktime,
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
    //选择发货方
    shipperClick() {
      this.travellingtraderVisible = true;
      this.$nextTick(() => {
        this.$refs.travellingtrader.init("shipper");
      });
    },
    //选择收货方
    receiverClick() {
      this.travellingtraderVisible = true;
      this.$nextTick(() => {
        this.$refs.travellingtrader.init("receiver");
      });
    },
    //选择承运单位
    carrierClick() {
      this.travellingtraderVisible = true;
      this.$nextTick(() => {
        this.$refs.travellingtrader.init("carrier");
      });
    },
    //客商选择返回值
    getTraderFromDialog(data) {
      let type = data.type;
      switch (type) {
        case "shipper":
          this.dataForm.shipperId = data.travellingTraderId;
          this.dataForm.shipperName = data.traderName;
          break;
        case "receiver":
          this.dataForm.receiverId = data.travellingTraderId;
          this.dataForm.receiverName = data.traderName;
          break;
        case "carrier":
          this.dataForm.carrierId = data.travellingTraderId;
          this.dataForm.carrierName = data.traderName;
          break;
      }
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
      this.dataForm.measurementId = data.measurementId;
      this.dataForm.measurementName = data.measurementName;
    },
    //选择车辆
    carinfoClick() {
      this.carinfoVisible = true;
      this.$nextTick(() => {
        this.$refs.carinfo.init();
      });
    },
    //货品选择返回值
    getCarinfoFromDialog(data) {
      this.dataForm.carInfoId = data.carInfoId;
      this.dataForm.carInfoName = data.carNumber;
    },
    //选择驾驶员
    driverClick() {
      this.driverVisible = true;
      this.$nextTick(() => {
        this.$refs.driver.init();
      });
    },
    //驾驶员选择返回值
    getDriverFromDialog(data) {
      this.dataForm.driverId = data.userId;
      this.dataForm.driverName = data.nickname;
    },
    //选择计量单位
    measurementClick() {
      this.measurementVisible = true;
      this.$nextTick(() => {
        this.$refs.measurement.init();
      });
    },
    //计量单位选择返回值
    getMeasurementFromDialog(data) {
      this.dataForm.measurementId = data.measurementId;
      this.dataForm.measurementName = data.measurementName;
    },
    //选择路线
    loadingPointClick(goodsId) {
      if(!goodsId){
        this.$message.error('请先选择货品再选择路线');
        return
      }
      this.routeVisible = true;
      this.$nextTick(() => {
        this.$refs.route.init(goodsId);
      });
    },
    //路线选择返回值
    getRouteFromDialog(data) {
      this.dataForm.loadingPoint = data.routeId;
      this.dataForm.loadingPointName = data.routeName;
    },
    //毛皮净关系
    weightChange(){
      this.dataForm.netWeight = this.dataForm.grossWeight - this.dataForm.tareWeight;
    }
  }
};
</script>
