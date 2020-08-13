<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-on:click.native="goodsClick" v-model="dataForm.goodsName" placeholder="货品"></el-input>
      </el-form-item>
      <el-form-item>
        <el-date-picker
          v-model="dataForm.iQueryDate"
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
      @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column type="index" width="50"></el-table-column>
      <!-- <el-table-column
        type="selection"
        header-align="center"
        align="center"
        width="50">
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
        label="货品">
      </el-table-column>
      <el-table-column
        prop="carTravelCode"
        header-align="center"
        align="center"
        label="运输编码">
      </el-table-column>
      <el-table-column
        prop="receiveDate"
        header-align="center"
        align="center"
        label="收货日期">
      </el-table-column>
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
        label="车牌">
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
        label="路线">
      </el-table-column>
      <el-table-column
        prop="inventoryType"
        header-align="center"
        align="center"
        label="出入库类型">
      </el-table-column>
      <!-- <el-table-column
        prop="netWeight"
        header-align="center"
        align="center"
        label="运输净重">
      </el-table-column> -->
      <el-table-column
        prop="inventoryWeight"
        header-align="center"
        align="center"
        label="库存净重">
      </el-table-column>
      <!-- <el-table-column
        prop="measurementId"
        header-align="center"
        align="center"
        label="计量单位ID">
      </el-table-column> -->
      <el-table-column
        prop="measurementName"
        header-align="center"
        align="center"
        label="计量单位">
      </el-table-column>
      <!-- <el-table-column
        prop="isStorage"
        header-align="center"
        align="center"
        label="入库">
      </el-table-column>
      <el-table-column
        prop="isOutbound"
        header-align="center"
        align="center"
        label="出库">
      </el-table-column> -->
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
    <goodsSlt v-if="goodsSltVisible" ref="goodsSlt" @func="getGoodsSltFromDialog"></goodsSlt>
  </div>
</template>

<script>
  import goodsSlt from './goods-select'
  export default {
    data () {
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
          goodsId: '',
          goodsName: '',
          iQueryDate: ''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        goodsSltVisible: false
      }
    },
    components: {
      goodsSlt
    },
    // activated () {
    //   this.getDataList()
    // },
    methods: {
      // 获取数据列表
      getDataList () {
        if(this.dataForm.iQueryDate == null || this.dataForm.iQueryDate == ''){
          this.$message.error('开始日期、结束日期不能为空');
          return
        }
        if(this.dataForm.goodsId == null || this.dataForm.goodsId == ''){
          this.$message.error('货品不能为空');
          return
        }
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/report/inventory/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'goodsId': this.dataForm.goodsId,
            'iBeginDate': this.dataForm.iQueryDate[0],
            'iEndDate': this.dataForm.iQueryDate[1]
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.dataList = data.page.list
            this.totalPage = data.page.totalCount
          } else {
            this.dataList = []
            this.totalPage = 0
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
      //选择貨品
      goodsClick() {
        this.goodsSltVisible = true;
        this.$nextTick(() => {
          this.$refs.goodsSlt.init();
        });
      },
      //貨品选择返回值
      getGoodsSltFromDialog(data) {
        console.log('返回data', data)
        this.dataForm.goodsId = data.goodsId;
        this.dataForm.goodsName = data.goodsName;
      }
    }
  }
</script>
