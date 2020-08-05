<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-on:click.native="carInfoClick" v-model="dataForm.carNames" placeholder="车辆" clearable></el-input>
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
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      style="width: 100%;">
      <el-table-column type="index" width="50"></el-table-column>
      <!-- <el-table-column
        prop="carInfoId"
        header-align="center"
        align="center"
        label="车辆ID">
      </el-table-column> -->
      <el-table-column
        prop="carNumber"
        header-align="center"
        align="center"
        label="车牌号">
      </el-table-column>
      <!-- <el-table-column
        prop="carName"
        header-align="center"
        align="center"
        label="车辆名称">
      </el-table-column> -->
      <!-- <el-table-column
        prop="receiveYearMonth"
        header-align="center"
        align="center"
        label="查看月">
      </el-table-column> -->
      <!-- <el-table-column
        prop="goodsId"
        header-align="center"
        align="center"
        label="货品ID">
      </el-table-column> -->
      <el-table-column
        prop="goodsName"
        header-align="center"
        align="center"
        label="货品名称">
      </el-table-column>
      <!-- <el-table-column
        prop="loadingPoint"
        header-align="center"
        align="center"
        label="路线ID">
      </el-table-column> -->
      <el-table-column
        prop="routeName"
        header-align="center"
        align="center"
        label="路线名称">
      </el-table-column>
      <el-table-column
        prop="carNum"
        header-align="center"
        align="center"
        label="车数">
      </el-table-column>
      <el-table-column
        prop="netWeightSum"
        header-align="center"
        align="center"
        label="数量">
      </el-table-column>
      <el-table-column
        prop="freightPrice"
        header-align="center"
        align="center"
        label="运费单价/元">
        <template slot-scope="scope">
          {{scope.row.freightPrice == 0 ? '' : scope.row.freightPrice }}
        </template>
      </el-table-column>
      <el-table-column
        prop="freightSum"
        header-align="center"
        align="center"
        label="运费合计/元">
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
    </el-pagination> -->
    <carinfoSlt v-if="carinfoSltVisible" ref="carinfoSlt" @func="getCarinfoSltFromDialog"></carinfoSlt>
  </div>
</template>

<script>
  import carinfoSlt from "./carinfo-select";
  export default {
    data () {
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
          carIds: '',
          carNames: '',
          qQueryDate: ''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false
      }
    },
    components: {
      carinfoSlt
    },
    // activated () {
    //   this.getDataList()
    // },
    methods: {
      // 获取数据列表
      getDataList () {
        if(this.dataForm.qQueryDate == null || this.dataForm.qQueryDate == ''){
          this.$message.error('开始日期、结束日期不能为空');
          return
        }
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/report/freightfee/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'carInfo': this.dataForm.carIds,
            'qBeginDate': this.dataForm.qQueryDate[0],
            'qEndDate': this.dataForm.qQueryDate[1]
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.dataList = data.page.list
            this.totalPage = data.page.totalCount
          } else {
            this.dataList = []
            this.totalPage = 0
            this.$message.error(data.msg)
          }
          this.dataListLoading = false
        })
      },
      // 每页数
      sizeChangeHandle (val) {
        this.pageSize = val
        this.pageIndex = 1
        this.getDataList()
      },
      // 当前页
      currentChangeHandle (val) {
        this.pageIndex = val
        this.getDataList()
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
        console.log('返回data', data)
        this.dataForm.carIds = data.carIds.join(',');
        this.dataForm.carNames = data.carNames.join(',');
      }
    }
  }
</script>
