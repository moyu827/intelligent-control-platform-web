<template>
  <div class="mod-user">
    <el-form :inline="true" :model="dataForm" ref="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        备件名称:
      </el-form-item>
      <el-form-item prop="sparePartsName">
        <el-input v-model="dataForm.sparePartsName" clearable placeholder="备件名称"></el-input>
      </el-form-item>
      <el-form-item>
        设备名称:
      </el-form-item>
      <el-form-item prop="deviceName">
        <el-input v-model="dataForm.deviceName" clearable placeholder="设备名称" ></el-input>
      </el-form-item>
      <el-form-item>
        消耗原因:
      </el-form-item>
      <el-form-item prop="consumeTypeName">
        <el-select v-model="dataForm.consumeTypeName" clearable  placeholder="请选择">
          <el-option
            v-for="item in options"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button @click="clearDataForm()">清空</el-button>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column
        type="index"
        :index="indexMethod"
        width="60"
        label="序号">

      </el-table-column>
      <el-table-column
        prop="sparePartsName"
        header-align="center"
        align="center"
        width="80"
        label="备件名称">
      </el-table-column>
      <el-table-column
        prop="consumptionNum"
        header-align="center"
        align="center"
        label="消耗数量">
      </el-table-column>
      <el-table-column
        prop="deviceName"
        header-align="center"
        align="center"
        label="设备名称">
      </el-table-column>
      <el-table-column
        prop="consumeTypeName"
        header-align="center"
        align="center"
        label="消耗原因">
      </el-table-column>
      <el-table-column
        prop="username"
        header-align="center"
        align="center"
        label="操作人">
      </el-table-column>
      <el-table-column
        prop="createTime"
        header-align="center"
        align="center"
        width="180"
        label="操作时间">
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
<script>
  import AddOrUpdate from './user-add-or-update'
  export default {
    data () {
      return {
        dataForm: {
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        options: [{
          value: '保养',
          label: '保养'
        }, {
          value: '维修',
          label: '维修'
        }]
        // value: ''
      }
    },
    components: {
      AddOrUpdate
    },
    activated () {
      this.getDataList()
    },
    methods: {
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
          url: this.$http.adornUrl('/backstage/GET/device-component-consumes'),
          method: 'get',
          params: this.$http.adornParams({
            'currentPage': this.pageIndex,
            'pageSize': this.pageSize,
            'sparePartsName': this.dataForm.sparePartsName,
            'deviceName': this.dataForm.deviceName,
            'consumeTypeName': this.dataForm.consumeTypeName
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
