<template>
  <div class="mod-user">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.sccjmc" placeholder="生产厂家名称" clearable></el-input>
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
      style="width: 100%;">
      <el-table-column
        prop="objId"
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
        prop="sccjbm"
        header-align="center"
        align="center"
        width="200"
        label="生产厂家编码">
      </el-table-column>
      <el-table-column
        prop="sccjmc"
        header-align="center"
        align="center"
        width="200"
        label="生产厂家名称">
      </el-table-column>
      <el-table-column
        prop="gj"
        header-align="center"
        align="center"
        width="100"
        label="国家">
      </el-table-column>
      <el-table-column
        prop="cs"
        header-align="center"
        align="center"
        width="100"
        label="城市">
      </el-table-column>
      <el-table-column
        prop="dz"
        header-align="center"
        align="center"
        width="200"
        label="地址">
      </el-table-column>
      <el-table-column
        prop="lxfs"
        header-align="center"
        align="center"
        width="200"
        label="联系方式">
      </el-table-column>
      <el-table-column
        prop="jyfw"
        header-align="center"
        align="center"
        width="200"
        label="经营范围">
      </el-table-column>
      <el-table-column
        prop="bz"
        header-align="center"
        align="center"
        width="200"
        label="备注">
      </el-table-column>
      <el-table-column
        header-align="center"
        align="center"
        label="操作 ">
        <template slot-scope="scope">
          <el-button @click="addOrUpdateHandle(scope.row.objId)" type="text" size="small">编辑</el-button>
          <el-button @click="deleteHandle(scope.row.objId)" type="text" size="small">删除</el-button>
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
    import AddOrUpdate from './mfrs-add-or-update'

    export default {
      data () {
        return {
          dataForm: {
            sccjmc: ''
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
            // 获取数据列表
        getDataList () {
          this.dataListLoading = true
          this.$http({
            url: this.$http.adornUrl('/standing/mfrs/GET/mfrs'),
            method: 'get',
            params: this.$http.adornParams({
              'currentPage': this.pageIndex,
              'pageSize': this.pageSize,
              'sccjmc': this.dataForm.sccjmc
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
        addOrUpdateHandle (id) {
          this.addOrUpdateVisible = true
          this.$nextTick(() => {
            this.$refs.addOrUpdate.init(id)
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
              url: this.$http.adornUrl(`/standing/mfrs/DELETE/mfrs`),
              method: 'delete',
              data: this.$http.adornData({
                'objId': id
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
