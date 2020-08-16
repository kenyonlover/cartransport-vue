<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input
          v-on:click.native="driverClick"
          v-model="dataForm.driverName"
          placeholder="驾驶员"
          clearable
        ></el-input>
      </el-form-item>
      <el-form-item>
        <el-date-picker
          v-model="dataForm.qQueryDate"
          type="daterange"
          align="right"
          value-format="yyyy-MM-dd"
          format="yyyy-MM-dd"
          unlink-panels
          range-separator="-"
          start-placeholder="开始日期"
          end-placeholder="结束日期"
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
    <el-table :data="dataList" border v-loading="dataListLoading" style="width: 100%;">
      <el-table-column type="index" width="50"></el-table-column>
      <!-- <el-table-column
        type="selection"
        header-align="center"
        align="center"
        width="50">
      </el-table-column>-->
      <!-- <el-table-column
        prop="driverId"
        header-align="center"
        align="center"
        label="驾驶员ID">
      </el-table-column>-->
      <el-table-column prop="nickname" header-align="center" align="center" label="驾驶员"></el-table-column>
      <!-- <el-table-column
        prop="carInfoId"
        header-align="center"
        align="center"
        label="车辆ID">
      </el-table-column>-->
      <el-table-column prop="carNumber" header-align="center" align="center" label="车牌号"></el-table-column>
      <!-- <el-table-column
        prop="carName"
        header-align="center"
        align="center"
        label="车辆名称">
      </el-table-column>-->
      <!-- <el-table-column
        prop="carType"
        header-align="center"
        align="center"
        label="车辆性质ID">
      </el-table-column>-->
      <el-table-column prop="carTypeName" header-align="center" align="center" label="车辆性质"></el-table-column>
      <!-- <el-table-column
        prop="receiveDate"
        header-align="center"
        align="center"
        label="到货日期">
      </el-table-column>-->
      <!-- <el-table-column
        prop="goodsId"
        header-align="center"
        align="center"
        label="货品ID">
      </el-table-column>-->
      <el-table-column prop="goodsName" header-align="center" align="center" label="货品"></el-table-column>
      <!-- <el-table-column
        prop="loadingPoint"
        header-align="center"
        align="center"
        label="路线ID">
      </el-table-column>-->
      <el-table-column prop="routeName" header-align="center" align="center" label="路线"></el-table-column>
      <el-table-column prop="carNum" header-align="center" align="center" label="车数"></el-table-column>
      <el-table-column prop="netWeightSum" header-align="center" align="center" label="数量"></el-table-column>
      <el-table-column prop="freightPrice" header-align="center" align="center" label="运费单价/元">
        <template slot-scope="scope">{{scope.row.freightPrice == 0 ? '' : scope.row.freightPrice }}</template>
      </el-table-column>
      <el-table-column prop="trailerPrice" header-align="center" align="center" label="工资拖车单价/元">
        <template slot-scope="scope">{{scope.row.trailerPrice == 0 ? '' : scope.row.trailerPrice }}</template>
      </el-table-column>
      <el-table-column prop="fourAxlePrice" header-align="center" align="center" label="工资四桥车单价/元">
        <template
          slot-scope="scope"
        >{{scope.row.fourAxlePrice == 0 ? '' : scope.row.fourAxlePrice }}</template>
      </el-table-column>
      <el-table-column prop="percentage" header-align="center" align="center" label="工资提成比例">
        <template slot-scope="scope">{{scope.row.percentage == 0 ? '' : scope.row.percentage }}</template>
      </el-table-column>
      <el-table-column prop="salary" header-align="center" align="center" label="工资合计"></el-table-column>
    </el-table>
    <!-- <el-pagination
      @size-change="sizeChangeHandle"
      @current-change="currentChangeHandle"
      :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]"
      :page-size="pageSize"
      :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>-->
    <driver v-if="driverVisible" ref="driver" @func="getDriverFromDialog"></driver>
  </div>
</template>

<script>
import driver from "./driver-select";
import { getNow } from "@/utils/datetime";
export default {
  data() {
    return {
      driverVisible: false,
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
        qDriverId: "",
        driverName: "",
        qQueryDate: "",
      },
      dataList: [],
      excelData: [],
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      dataListLoading: false,
    };
  },
  components: {
    driver,
  },
  // activated () {
  //   this.getDataList()
  // },
  methods: {
    // 获取数据列表
    getDataList() {
      if (this.dataForm.qQueryDate == null || this.dataForm.qQueryDate == "") {
        this.$message.error("开始日期、结束日期不能为空");
        return;
      }
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/report/salaryfee/list"),
        method: "get",
        params: this.$http.adornParams({
          page: this.pageIndex,
          limit: this.pageSize,
          qDriverId: this.dataForm.qDriverId,
          qBeginDate: this.dataForm.qQueryDate[0],
          qEndDate: this.dataForm.qQueryDate[1],
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
    //选择驾驶员
    driverClick() {
      this.driverVisible = true;
      this.$nextTick(() => {
        this.$refs.driver.init();
      });
    },
    //驾驶员选择返回值
    getDriverFromDialog(data) {
      this.dataForm.qDriverId = data.driverIds.join(",");
      this.dataForm.driverName = data.driverNames.join(",");
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
          "驾驶员",
          "车牌号",
          "车辆性质",
          "货品",
          "路线",
          "车数",
          "数量",
          "运费单价/元",
          "工资拖车单价/元",
          "工资四桥车单价/元",
          "工资提成比例",
          "工资合计",
        ]; // 导出的表头名信息
        const filterVal = [
          "nickname",
          "carNumber",
          "carTypeName",
          "goodsName",
          "routeName",
          "carNum",
          "netWeightSum",
          "freightPrice",
          "trailerPrice",
          "fourAxlePrice",
          "percentage",
          "salary",
        ]; // 导出的表头字段名，需要导出表格字段名
        const list = that.excelData;
        const data = that.formatJson(filterVal, list);

        let now = getNow();
        export_json_to_excel(tHeader, data, "驾驶员工资报表下载 " + now); // 导出的表格名称，根据需要自己命名
      });
    },
    //格式转换，直接复制即可
    formatJson(filterVal, jsonData) {
      return jsonData.map((v) => filterVal.map((j) => v[j]));
    },
  },
};
</script>
