<template>
  <el-dialog :title="'驾驶员'" :close-on-click-modal="false" :visible.sync="visible" append-to-body>
    <el-table
      ref="singleTable"
      :data="tableData"
      highlight-current-row
      @selection-change="selectionChangeHandle"
      style="width: 100%"
      height="350"
    >
      <!-- <el-table-column type="index" width="50"></el-table-column> -->
      <el-table-column
        type="selection"
        header-align="center"
        align="center"
        width="50">
      </el-table-column>
      <el-table-column property="username" label="用户名"></el-table-column>
      <el-table-column property="nickname" label="昵称"></el-table-column>
      <el-table-column property="mobile" label="手机号"></el-table-column>
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
          url: this.$http.adornUrl(`/sys/user/allDriver`),
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
      // if(this.dataListSelections.length === 0){
      //   this.$message.error('必须选择数据才能确定');
      //   return
      // }
      this.selectIds = this.dataListSelections.map(item => {
        return item.userId
      })
      this.selectNames = this.dataListSelections.map(item => {
        return item.nickname
      })
      let respMap = {};
      respMap.driverIds = this.selectIds;
      respMap.driverNames = this.selectNames;
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