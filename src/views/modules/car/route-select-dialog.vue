<template>
  <el-dialog :title="'车牌'" :close-on-click-modal="false" :visible.sync="visible" :before-close="handleDialogClose" append-to-body>
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
      <el-table-column property="routeCode" label="路线编码" width="180%"></el-table-column>
      <el-table-column property="routeName" label="路线" width="180%"></el-table-column>
      <el-table-column property="goodsName" label="货品"></el-table-column>
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
      currentRow: null
    };
  },

  methods: {
    init (goodsId) {
      this.visible = true;
      this.$nextTick(() => {
        this.$http({
          url: this.$http.adornUrl(`/car/route/select/${goodsId}`),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({ data }) => {
          if (data && data.code === 0) {
            this.tableData = data.list;
          } else {
            this.$message.error(data.msg);
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
      this.tableData = [];
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
    handleDialogClose() {
      this.tableData = [];
      this.visible = false;
    }
  }
};
</script>