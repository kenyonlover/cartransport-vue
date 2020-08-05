<template>
  <el-dialog :title="'车牌'" :close-on-click-modal="false" :visible.sync="visible" append-to-body>
    <el-table
      ref="singleTable"
      :data="tableData"
      highlight-current-row
      @selection-change="selectionChangeHandle"
      style="width: 100%"
      height="350"
    >
      <el-table-column
        type="selection"
        header-align="center"
        align="center"
        width="50">
      </el-table-column>
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
      visible: false,
      tableData: [],
      dataListSelections: [],
      selectIds:[],
      selectNames:[]
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
    cancle() {
      this.visible = false;
    },
    sure() {
      this.selectIds = this.dataListSelections.map(item => {
        return item.carInfoId
      })
      this.selectNames = this.dataListSelections.map(item => {
        return item.carNumber
      })
      let respMap = {};
      respMap.carIds = this.selectIds;
      respMap.carNames = this.selectNames;
      this.$emit('func', respMap);
      this.cancle();
    },
    // 多选
    selectionChangeHandle (val) {
      this.dataListSelections = val
    }
  }
};
</script>