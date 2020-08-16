<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input
          v-on:click.native="carInfoClick"
          v-model="dataForm.carNames"
          placeholder="车辆"
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
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
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
        prop="carId"
        header-align="center"
        align="center"
        label="车辆ID">
      </el-table-column>-->
      <el-table-column prop="carNumber" header-align="center" align="center" label="车牌号"></el-table-column>
      <!-- <el-table-column
        prop="expendId"
        header-align="center"
        align="center"
        label="支出类型ID">
      </el-table-column>-->
      <el-table-column prop="expendName" header-align="center" align="center" label="支出类型"></el-table-column>
      <el-table-column prop="expendDate" header-align="center" align="center" label="支出日期"></el-table-column>
      <el-table-column prop="money" header-align="center" align="center" label="金额">
        <template slot-scope="scope">{{scope.row.money == 0 ? '' : scope.row.money }}</template>
      </el-table-column>
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
    <carinfoSlt v-if="carinfoSltVisible" ref="carinfoSlt" @func="getCarinfoSltFromDialog"></carinfoSlt>
  </div>
</template>

<script>
import carinfoSlt from "./carinfo-select";
import { getNow } from "@/utils/datetime";
export default {
  data() {
    return {
      carinfoSltVisible: false,
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
        carIds: "",
        carNames: "",
        qQueryDate: "",
      },
      dataList: [],
      excelData: [],
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      dataListLoading: false,
      dataListSelections: [],
    };
  },
  components: {
    carinfoSlt,
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
        url: this.$http.adornUrl("/report/carexpends/list"),
        method: "get",
        params: this.$http.adornParams({
          page: this.pageIndex,
          limit: this.pageSize,
          carInfo: this.dataForm.carIds,
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
    //选择车辆
    carInfoClick() {
      this.carinfoSltVisible = true;
      this.$nextTick(() => {
        this.$refs.carinfoSlt.init();
      });
    },
    //车辆选择返回值
    getCarinfoSltFromDialog(data) {
      console.log("返回data", data);
      this.dataForm.carIds = data.carIds.join(",");
      this.dataForm.carNames = data.carNames.join(",");
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
          "车牌号",
          "支出类型",
          "支出日期",
          "金额",
        ]; // 导出的表头名信息
        const filterVal = [
          "carNumber",
          "expendName",
          "expendDate",
          "money",
        ]; // 导出的表头字段名，需要导出表格字段名
        const list = that.excelData;
        const data = that.formatJson(filterVal, list);

        let now = getNow();
        export_json_to_excel(tHeader, data, "车辆支出统计报表下载 " + now); // 导出的表格名称，根据需要自己命名
      });
    },
    //格式转换，直接复制即可
    formatJson(filterVal, jsonData) {
      return jsonData.map((v) => filterVal.map((j) => v[j]));
    },
  },
};
</script>
