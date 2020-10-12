<template>
  <el-dialog :title="'车牌'" :close-on-click-modal="false" :visible.sync="visible" append-to-body>

    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.carnum" placeholder="车牌号" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
      </el-form-item>
    </el-form>

    <el-table
      ref="singleTable"
      :data="tableData"
      highlight-current-row
      @current-change="handleCurrentChange"
      @row-dblclick="handleRowDblclick"
      style="width: 100%"
      height="350"
    >
      <el-table-column type="index" width="50"></el-table-column>
      <el-table-column property="carCode" label="车辆编码" width="180%"></el-table-column>
      <el-table-column property="carName" label="车辆名称" width="180%"></el-table-column>
      <el-table-column property="carNumber" label="车牌号"></el-table-column>
      <el-table-column property="note" label="备注"></el-table-column>
    </el-table>

    <span slot="footer" class="dialog-footer">
      <el-button @click="cancle()">取消</el-button>
      <el-button type="primary" @click="sure()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
export default {
  data() {
    return {
      dataForm: {
        carnum: ''
      },
      visible: false,
      tableData: [],
      currentRow: null
    };
  },

  methods: {
    init () {
      this.visible = true;
      this.$nextTick(() => {
        this.$http({
          url: this.$http.adornUrl(`/car/carinfo/allSlt`),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({ data }) => {
          if (data && data.code === 0) {
            this.tableData = data.list;
          }
        });
      });
    },
    setCurrent (row) {
      this.$refs.singleTable.setCurrentRow(row);
    },
    handleCurrentChange(val) {
      this.currentRow = val;
    },
    cancle() {
      this.visible = false;
    },
    sure() {
      this.$emit('func', this.currentRow);
      this.cancle();
    },
    handleRowDblclick(row){
      this.currentRow = row;
      this.sure();
    },
    // 获取数据列表
    getDataList () {
      this.visible = true
      this.$http({
        url: this.$http.adornUrl('/car/carinfo/querySlt'),
        method: 'get',
        params: this.$http.adornParams({
          'carnum': this.dataForm.carnum
        })
      }).then(({data}) => {
        if (data && data.code === 0) {
          this.tableData = data.list;
        } else {
          this.tableData = []
        }
      })
    }
  }
};
</script>