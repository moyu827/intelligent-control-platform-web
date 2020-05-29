<template>
  <div>
    <el-header style="text-align: center"  v-text="myHeader"></el-header>
    <div>
      <el-table
        border
        :data="tableData"
        :span-method="objectSpanMethod"
        v-loading="dataListLoading"
        class="tb-edit"
        style="width: 100%"
        highlight-current-row
      >
        <el-table-column :label="item.label" v-for="(item,index) in tableNotEditHead" :key="index">
          <template slot-scope="scope">
            <span>{{ scope.row[item.prop] }}</span>
          </template>
        </el-table-column>

        <el-table-column :label="item.label" v-for="(item,index) in tableEditHeader" :key="index">
          <template slot-scope="scope">
            <el-input
              size="small"
              v-model="scope.row[item.prop]"
            ></el-input>
            <span>{{ scope.row[item.prop] }}</span>
          </template>
        </el-table-column>
      </el-table>
    </div>
    <el-footer style="text-align: center"> <el-button type="primary" @click="submit">提交</el-button></el-footer>
  </div>
</template>
<script>
    export default {
        data () {
            return {
                dataListLoading: false,
                myHeader: '',
                tableEditHeader: [],
                tableNotEditHead: [],
                tableData: []
            }
        },
        activated () {
            this.getTableInfo()
            this.getTableData()
        },
        methods: {
            objectSpanMethod ({ row, column, rowIndex, columnIndex }) {
                if (columnIndex === 0) {
                    if (rowIndex % 4 === 0) {
                        return {
                            rowspan: 4,
                            colspan: 1
                        }
                    } else {
                        return {
                            rowspan: 0,
                            colspan: 0
                        }
                    }
                }
            },
            submit () {
                console.log(this.tableData)
            },
            getTableInfo () {
                this.$http({
                    url: this.$http.adornUrl('/bus/test/GET/tableInfo'),
                    method: 'get',
                    params: this.$http.adornParams({
                    })
                }).then(({data}) => {
                    if (data.status === 'success') {
                        this.myHeader = data.rows.title
                        this.tableEditHeader = data.rows.editColumns
                        this.tableNotEditHead = data.rows.notEditColumns
                    } else {
                        this.myHeader = ''
                        this.tableEditHeader = []
                        this.tableNotEditHead = []
                    }
                })
            },
            getTableData () {
                this.dataListLoading = true
                this.$http({
                    url: this.$http.adornUrl('/bus/test/GET/tableData'),
                    method: 'get',
                    params: this.$http.adornParams({
                    })
                }).then(({data}) => {
                    if (data.status === 'success') {
                        this.tableData = data.rows
                    } else {
                        this.tableData = []
                    }
                    this.dataListLoading = false
                })
            }
        }
    }
</script>
<style >
  * {
    margin: 0;
    padding: 0;
  }
  body {
    font-family: Helvetica Neue, Helvetica, PingFang SC, Hiragino Sans GB,
    Microsoft YaHei, SimSun, sans-serif;
    overflow: auto;
    font-weight: 400;
    -webkit-font-smoothing: antialiased;
  }
  .tb-edit .el-input {
    display: none;
  }
  .tb-edit .current-row .el-input {
    display: block;
  }
  .tb-edit .current-row .el-input + span {
    display: none;
  }
  .el-table td,
  .el-table th {
    text-align: center !important;
  }
</style>
