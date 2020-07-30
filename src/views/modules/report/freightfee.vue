<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.carInfo" placeholder="车辆" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-input v-model="dataForm.viewMonth" placeholder="月份" clearable></el-input>
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
      <el-table-column
        prop="receiveYearMonth"
        header-align="center"
        align="center"
        label="查看月">
      </el-table-column>
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
  </div>
</template>

<script>
  export default {
    data () {
      return {
        dataForm: {
          carInfo: '',
          viewMonth: ''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false
      }
    },
    // activated () {
    //   this.getDataList()
    // },
    methods: {
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/report/freightfee/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'carInfo': this.dataForm.carInfo,
            'viewMonth': this.dataForm.viewMonth
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
      }
    }
  }
</script>
