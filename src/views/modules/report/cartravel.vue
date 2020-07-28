<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.shipperName" placeholder="发货方" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.receiverName" placeholder="收货方" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.carrierName" placeholder="承运单位" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.goodsName" placeholder="货品" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.carInfoName" placeholder="车牌" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.driverName" placeholder="驾驶员" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.loadingPointName" placeholder="路线" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-date-picker
          v-model="dataForm.receiveDate"
          type="daterange"
          align="right"
          value-format="yyyy-MM-dd"
          format="yyyy-MM-dd"
          unlink-panels
          range-separator="-"
          start-placeholder="收货开始日期"
          end-placeholder="收货结束日期"
          :picker-options="pickerOptions"
        ></el-date-picker>
      </el-form-item>
      <el-form-item>
        <el-date-picker
          v-model="dataForm.deliveryDate"
          type="daterange"
          align="right"
          value-format="yyyy-MM-dd"
          format="yyyy-MM-dd"
          unlink-panels
          range-separator="-"
          start-placeholder="发货开始日期"
          end-placeholder="发货结束日期"
          :picker-options="pickerOptions"
        ></el-date-picker>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
      </el-form-item>
      <el-form-item>
        <el-button @click="downloadExcel()">下载当前页所有数据</el-button>
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      show-summary
      :summary-method="getSummaries"
      v-loading="dataListLoading"
      style="width: 100%;"
    >
      <el-table-column type="index" width="50"></el-table-column>
      <!-- <el-table-column
        type="selection"
        header-align="center"
        align="center"
        width="50">
      </el-table-column>-->
      <!-- <el-table-column
        prop="carTravelId"
        header-align="center"
        align="center"
        label="ID">
      </el-table-column>-->
      <el-table-column prop="carTravelCode" header-align="center" align="center" label="运输编码"></el-table-column>
      <!-- <el-table-column
        prop="shipperId"
        header-align="center"
        align="center"
        label="发货方_ID">
      </el-table-column>-->
      <el-table-column prop="shipperName" header-align="center" align="center" label="发货方"></el-table-column>
      <!-- <el-table-column
        prop="receiverId"
        header-align="center"
        align="center"
        label="收货方_ID">
      </el-table-column>-->
      <el-table-column prop="receiverName" header-align="center" align="center" label="收货方"></el-table-column>
      <!-- <el-table-column
        prop="carrierId"
        header-align="center"
        align="center"
        label="承运单位_ID">
      </el-table-column>-->
      <el-table-column prop="carrierName" header-align="center" align="center" label="承运单位"></el-table-column>
      <!-- <el-table-column
        prop="goodsId"
        header-align="center"
        align="center"
        label="货品_ID">
      </el-table-column>-->
      <el-table-column prop="goodsName" header-align="center" align="center" label="货品"></el-table-column>
      <!-- <el-table-column
        prop="carInfoId"
        header-align="center"
        align="center"
        label="车牌_ID">
      </el-table-column>-->
      <el-table-column prop="carInfoName" header-align="center" align="center" label="车牌"></el-table-column>
      <!-- <el-table-column
        prop="driverId"
        header-align="center"
        align="center"
        label="驾驶员_ID">
      </el-table-column>-->
      <el-table-column prop="driverName" header-align="center" align="center" label="驾驶员"></el-table-column>
      <el-table-column prop="receiveDate" header-align="center" align="center" label="收货日期"></el-table-column>
      <el-table-column prop="grossWeight" header-align="center" align="center" label="毛重"></el-table-column>
      <el-table-column prop="tareWeight" header-align="center" align="center" label="皮重"></el-table-column>
      <el-table-column prop="netWeight" header-align="center" align="center" label="净重"></el-table-column>
      <!-- <el-table-column
        prop="measurementId"
        header-align="center"
        align="center"
        label="计量单位_ID">
      </el-table-column>-->
      <el-table-column prop="measurementName" header-align="center" align="center" label="计量单位"></el-table-column>
      <el-table-column prop="deliveryDate" header-align="center" align="center" label="发货方日期"></el-table-column>
      <el-table-column prop="deliveryNetWeight" header-align="center" align="center" label="发货方净重"></el-table-column>
      <!-- <el-table-column
        prop="loadingPoint"
        header-align="center"
        align="center"
        label="路线_ID">
      </el-table-column>-->
      <el-table-column prop="loadingPointName" header-align="center" align="center" label="路线"></el-table-column>
      <!-- <el-table-column
        prop="dischargePoint"
        header-align="center"
        align="center"
        label="卸货点">
      </el-table-column>-->
      <el-table-column prop="loadingFee" header-align="center" align="center" label="装车费/元"></el-table-column>
      <el-table-column prop="weighingFee" header-align="center" align="center" label="过磅费/元"></el-table-column>
      <el-table-column prop="isStorage" header-align="center" align="center" label="是否入库">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.isStorage === 1" size="small" type="success">是</el-tag>
          <el-tag v-else size="small">否</el-tag>
        </template>
      </el-table-column>
      <el-table-column prop="isOutbound" header-align="center" align="center" label="是否出库">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.isOutbound === 1" size="small" type="success">是</el-tag>
          <el-tag v-else size="small">否</el-tag>
        </template>
      </el-table-column>
      <el-table-column prop="isCheck" header-align="center" align="center" label="是否核对">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.isCheck === 1" size="small" type="success">是</el-tag>
          <el-tag v-else size="small">否</el-tag>
        </template>
      </el-table-column>
      <el-table-column prop="note" header-align="center" align="center" label="备注" width="200%"></el-table-column>
      <!-- <el-table-column
        prop="creater"
        header-align="center"
        align="center"
        label="创建人_ID">
      </el-table-column>-->
      <el-table-column prop="createrName" header-align="center" align="center" label="创建人"></el-table-column>
      <el-table-column prop="createtime" header-align="center" align="center" label="创建时间"></el-table-column>
      <!-- <el-table-column
        prop="reviser"
        header-align="center"
        align="center"
        label="修改人_ID">
      </el-table-column>-->
      <el-table-column prop="reviserName" header-align="center" align="center" label="最后操作人"></el-table-column>
      <el-table-column prop="revisetime" header-align="center" align="center" label="最后操作时间"></el-table-column>
      <!-- <el-table-column
        prop="checker"
        header-align="center"
        align="center"
        label="核对人_ID">
      </el-table-column>-->
      <el-table-column prop="checkerName" header-align="center" align="center" label="核对操作人"></el-table-column>
      <el-table-column prop="checktime" header-align="center" align="center" label="核对操作时间"></el-table-column>
      <!-- <el-table-column
        prop="deleted"
        header-align="center"
        align="center"
        label="删除标记">
      </el-table-column>-->
      <!-- <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="viewHandle(scope.row.carTravelId)">查看</el-button>
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.carTravelId)">修改</el-button>
          <el-button type="text" size="small" @click="copyHandle(scope.row.carTravelId)">复制</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.carTravelId)">删除</el-button>
        </template>
      </el-table-column>-->
    </el-table>
    <el-pagination
      @size-change="sizeChangeHandle"
      @current-change="currentChangeHandle"
      :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100, 500, 1000, 2000, 5000]"
      :page-size="pageSize"
      :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper"
    ></el-pagination>
  </div>
</template>

<script>
import { getNow } from '@/utils/datetime'
export default {
  data() {
    return {
      pickerOptions: {
        shortcuts: [
          {
            text: "最近一周",
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 7);
              picker.$emit("pick", [start, end]);
            },
          },
          {
            text: "最近一个月",
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 30);
              picker.$emit("pick", [start, end]);
            },
          },
          {
            text: "最近三个月",
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 90);
              picker.$emit("pick", [start, end]);
            },
          },
        ],
      },
      dataForm: {
        shipperName: "",
        receiverName: "",
        carrierName: "",
        goodsName: "",
        carInfoName: "",
        driverName: "",
        loadingPointName: "",
        receiveDate: "",
        deliveryDate: "",
      },
      dataList: [],
      excelData: [],
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      dataListLoading: false,
      dataListSelections: [],
      addOrUpdateVisible: false,
      viewVisible: false,
      copyVisible: false,
    };
  },
  components: {},
  activated() {
    this.getDataList();
  },
  methods: {
    // 获取数据列表
    getDataList() {
      this.dataListLoading = true;
      let recDate =
        this.dataForm.receiveDate == null || this.dataForm.receiveDate == ""
          ? ""
          : this.dataForm.receiveDate.join(",");
      let delDate =
        this.dataForm.deliveryDate == null || this.dataForm.deliveryDate == ""
          ? ""
          : this.dataForm.deliveryDate.join(",");
      this.$http({
        url: this.$http.adornUrl("/car/cartravel/list"),
        method: "get",
        params: this.$http.adornParams({
          page: this.pageIndex,
          limit: this.pageSize,
          shipperName: this.dataForm.shipperName,
          receiverName: this.dataForm.receiverName,
          carrierName: this.dataForm.carrierName,
          goodsName: this.dataForm.goodsName,
          carInfoName: this.dataForm.carInfoName,
          driverName: this.dataForm.driverName,
          loadingPointName: this.dataForm.loadingPointName,
          receiveDate: recDate,
          deliveryDate: delDate,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataList = data.page.list;
          this.totalPage = data.page.totalCount;
        } else {
          this.dataList = [];
          this.totalPage = 0;
          this.$message.error(data.msg);
        }
        this.dataListLoading = false;
      });
    },
    // 每页数
    sizeChangeHandle(val) {
      this.pageSize = val;
      this.pageIndex = 1;
      this.getDataList();
    },
    // 当前页
    currentChangeHandle(val) {
      this.pageIndex = val;
      this.getDataList();
    },
    //合计
    getSummaries(param) {
      const { columns, data } = param;
      const sums = [];
      columns.forEach((column, index) => {
        if (index === 0) {
          sums[index] = "合计";
          return;
        }
        if (index > 16) {
          return;
        }
        const values = data.map((item) => Number(item[column.property]));
        if (!values.every((value) => isNaN(value))) {
          sums[index] = values.reduce((prev, curr) => {
            const value = Number(curr);
            if (!isNaN(value)) {
              return prev + curr;
            } else {
              return prev;
            }
          }, 0);
          sums[index] = sums[index].toFixed(2);
        }
      });

      return sums;
    },
    //下载当前页所有数据
    downloadExcel() {
      this.$confirm("确定下载列表文件?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(() => {
          this.excelData = this.dataList; //你要导出的数据list。
          this.export2Excel();
        })
        .catch(() => {});
    },
    //数据写入excel
    export2Excel() {
      var that = this;
      require.ensure([], () => {
        const { export_json_to_excel } = require("@/excel/export2Excel"); //这里必须使用绝对路径，使用@/+存放export2Excel的路径
        const tHeader = [
          "运输编码",
          "发货方",
          "收货方",
          "承运单位",
          "货品",
          "车牌",
          "驾驶员",
          "收货日期",
          "毛重",
          "皮重",
          "净重",
          "计量单位",
          "发货方日期",
          "发货方净重",
          "路线",
          "装车费/元",
          "过磅费/元",
          "备注",
          "创建人",
          "创建时间",
        ]; // 导出的表头名信息
        const filterVal = [
          "carTravelCode",
          "shipperName",
          "receiverName",
          "carrierName",
          "goodsName",
          "carInfoName",
          "driverName",
          "receiveDate",
          "grossWeight",
          "tareWeight",
          "netWeight",
          "measurementName",
          "deliveryDate",
          "deliveryNetWeight",
          "loadingPointName",
          "loadingFee",
          "weighingFee",
          "note",
          "createrName",
          "createtime",
        ]; // 导出的表头字段名，需要导出表格字段名
        const list = that.excelData;
        const data = that.formatJson(filterVal, list);

        let now = getNow();
        export_json_to_excel(tHeader, data, "车辆运输报表下载 " + now); // 导出的表格名称，根据需要自己命名
      });
    },
    //格式转换，直接复制即可
    formatJson(filterVal, jsonData) {
      return jsonData.map((v) => filterVal.map((j) => v[j]));
    },
  },
};
</script>
