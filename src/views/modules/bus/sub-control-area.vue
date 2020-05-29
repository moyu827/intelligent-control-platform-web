<template>
  <div class="mod-user">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.name" placeholder="子控区名称" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button type="primary" @click="addOrUpdateHandle()">新增</el-button>
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 800px;">
      <el-table-column
        prop="id"
        v-if="false"
        header-align="center"
        align="center"
        width="80"
        label="ID">
      </el-table-column>
      <el-table-column
        type="index"
        :index="indexMethod"
        width="60"
        label="序号">
      </el-table-column>
      <el-table-column
        prop="name"
        header-align="center"
        align="center"
        width="200"
        label="子控区名称">
      </el-table-column>
      <el-table-column
        prop="type"
        header-align="center"
        align="center"
        width="200"
        :formatter="formatterType"
        label="子控区类别">
      </el-table-column>
      <el-table-column
        prop="parentName"
        header-align="center"
        align="center"
        width="200"
        :formatter="formatterParentName"
        label="上级单位">
      </el-table-column>
      <el-table-column
        header-align="center"
        align="center"
        label="操作 ">
        <template slot-scope="scope">
          <el-button @click="addOrUpdateHandle(scope.row.id,scope.row.type)" type="text" size="small">编辑</el-button>
          <el-button @click="deleteHandle(scope.row.id,scope.row.type)" type="text" size="small">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="sizeChangeHandle"
      @current-change="currentChangeHandle"
      :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]"
      :page-size="pageSize"
      :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
  </div>
</template>
<style>
  .site-wrapper .el-pagination {
    margin-top: 15px !important;
    text-align: left !important;
  }
</style>
<script>
    // import AddOrUpdate from './user-add-or-update'
    import AddOrUpdate from './sub-control-area-add-or-update'

    export default {
      data () {
        return {
          dataForm: {
            name: ''
          },
          dataList: [],
          pageIndex: 1,
          pageSize: 10,
          totalPage: 0,
          dataListLoading: false,
          dataListSelections: [],
          addOrUpdateVisible: false
        }
      },
      components: {
        AddOrUpdate
      },
      activated () {
        this.getDataList()
      },
      methods: {
        formatterType (row, column) {
            // eslint-disable-next-line no-unused-vars
          let rowType = row.type
          if (rowType === 1) {
            return '省级'
          } else if (rowType === 2) {
            return '市级'
          } else if (rowType === 3) {
            return '县级'
          }
        },
        formatterParentName (row, column) {
            // eslint-disable-next-line no-unused-vars
          const rowParentName = row.parentName
          if (undefined === rowParentName || rowParentName === '') {
            return '无'
          } else {
            return rowParentName
          }
        },
            // 获取数据列表
        getDataList () {
          this.dataListLoading = true
          this.$http({
            url: this.$http.adornUrl('/bus/sub-control-area/GET/subControlAreas'),
            method: 'get',
            params: this.$http.adornParams({
              'currentPage': this.pageIndex,
              'pageSize': this.pageSize,
              'name': this.dataForm.name
            })
          }).then(({data}) => {
            if (data.status === 'success') {
              this.dataList = data.rows
              this.totalPage = data.total
            } else {
              this.dataList = []
              this.totalPage = 10
            }
            this.dataListLoading = false
          })
        },
        indexMethod (index) {
          return index + 1
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
            // 多选
        selectionChangeHandle (val) {
          this.dataListSelections = val
        },

            // 新增 / 修改
        addOrUpdateHandle (id,type) {
          this.addOrUpdateVisible = true
          this.$nextTick(() => {
            this.$refs.addOrUpdate.init(id,type)
          })
        },
            // 删除
        deleteHandle (id) {
          this.$confirm(`确定删除?`, '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(() => {
            this.$http({
              url: this.$http.adornUrl(`/bus/sub-control-area/DELETE/subControlArea`),
              method: 'delete',
              data: this.$http.adornData({
                'id': id
              })
            }).then(({data}) => {
              if (data.status === 'success') {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500
                })
                this.getDataList()
              } else {
                this.$message.error(data.message)
              }
            })
          }).catch(() => {
          })
        }
      }
    }
</script>
