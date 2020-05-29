<template>
  <div>
    <el-form :inline="true" :model="dataForm" ref="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        备件名称
      </el-form-item>
      <el-form-item prop="deviceName">
        <el-input v-model="dataForm.deviceName" clearable placeholder="备件名称" ></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="clearDataForm()">清空</el-button>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
      </el-form-item>
      <el-form-item>
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
        type="id"
        v-if="false"
        width="60"
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
        width="120"
        label="备件名称">
      </el-table-column>
      <el-table-column
        prop="model"
        header-align="center"
        align="center"
        label="规格型号">
      </el-table-column>
      <el-table-column
        prop="code"
        :show-overflow-tooltip="true"
        header-align="center"
        align="center"
        label="备件编号">
      </el-table-column>
      <el-table-column
        prop="manufacturer"
        header-align="center"
        align="center"
        label="生产厂家">
      </el-table-column>
      <el-table-column
        prop="storageNum"
        header-align="center"
        align="center"
        label="库存数量">
      </el-table-column>
      <el-table-column
        header-align="center"
        align="center"
        label="操作 ">
        <template slot-scope="scope">
          <el-button @click="addOrUpdateHandle(scope.row.id)" type="text" size="small">编辑</el-button>
          <el-button @click="deleteSpare(scope.row.id)" type="text" size="small">删除</el-button>
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
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible"  ref="addOrUpdate" @refreshDataList="getDataList" @refReload="reload"></add-or-update>
  </div>
</template>
<style>
  body .el-table th.gutter{
    display: table-cell!important;
  }
</style>
<script>
  import AddOrUpdate from './spare-add-or-update'
  export default {
    data () {
      return {
        dataForm:{
        },
        isRouterAlive: true,
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
      reload () {
        window.location.reload()
      },
      addOrUpdateHandle (id) {
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(id)
        })
      },
      deleteSpare (id) {
        this.$http({
          url: this.$http.adornUrl(`/backstage/DELETE/reserveComponent`),
          method: 'post',
          data: this.$http.adornData({
            'id': id,
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
      },
      indexMethod (index) {
        return index + 1
      },
      clearDataForm () {
        this.$refs.dataForm.resetFields()
      },
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.dataList = []
        this.$http({
          url: this.$http.adornUrl('/backstage/GET/reserveComponents'),
          method: 'post',
          params: this.$http.adornParams({
            'currentPage': this.pageIndex,
            'pageSize': this.pageSize,
            'name': this.dataForm.deviceName
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
      }

    }
  }
</script>
