<template>
  <el-dialog :title="'客商'" :close-on-click-modal="false" :visible.sync="visible" append-to-body>
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
      <el-table-column property="traderCode" label="客商编码" width="180%"></el-table-column>
      <el-table-column property="traderName" label="客商名称"></el-table-column>
      <el-table-column property="linkman" label="联系人"></el-table-column>
      <el-table-column property="linkInformation" label="联系方式"></el-table-column>
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
      condition: "",
      tableData: [],
      currentRow: null
    };
  },

  methods: {
    init(condition) {
      this.visible = true;
      this.condition = condition;
      this.$nextTick(() => {
        this.$http({
          url: this.$http.adornUrl(`/car/travellingtrader/allSlt/${condition}`),
          method: "get",
          params: this.$http.adornParams()
        }).then(({ data }) => {
          if (data && data.code === 0) {
            this.tableData = data.list;
          }
        });
      });
    },
    setCurrent(row) {
      this.$refs.singleTable.setCurrentRow(row);
    },
    handleCurrentChange(val) {
      this.currentRow = val;
    },
    cancle() {
      this.visible = false;
    },
    sure() {
      this.currentRow.type = this.condition;
      this.$emit("func", this.currentRow);
      this.cancle();
    },
    handleRowDblclick(row) {
      this.currentRow = row;
      this.sure();
    }
  }
};
</script>