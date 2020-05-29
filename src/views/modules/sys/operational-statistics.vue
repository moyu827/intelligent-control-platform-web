<template>
  <div>
    <el-form :inline="true" :model="dataForm" ref="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        操作类型:
      </el-form-item>
      <el-form-item prop="operationTypeCode">
        <el-select v-model="dataForm.operationTypeCode" clearable  placeholder="请选择">
          <el-option
            v-for="item in options"
            :key="item.code"
            :label="item.name"
            :value="item.code">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        设备名称:
      </el-form-item>
      <el-form-item prop="deviceName">
        <el-input v-model="dataForm.deviceName" clearable placeholder="设备名称" ></el-input>
      </el-form-item>
      <el-form-item>
        操作人
      </el-form-item>
      <el-form-item prop="username">
        <el-input v-model="dataForm.username" clearable placeholder="操作人" ></el-input>
      </el-form-item>
      <el-form-item>
        操作时间
      </el-form-item>
      <el-form-item prop="operationTime">
        <el-date-picker
          v-model="dataForm.operationTime"
          type="datetimerange"
          :picker-options="pickerOptions"
          @change="changeDate"
          format="yyyy-MM-dd HH:mm:ss"
          value-format="yyyy-MM-dd HH:mm:ss"
          range-separator="至"
          start-placeholder="开始日期"
          end-placeholder="结束日期"
          align="right">
        </el-date-picker>
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
        prop="operationTypeName"
        header-align="center"
        align="center"
        width="80"
        label="操作类型">
      </el-table-column>
      <el-table-column
        prop="equipmentName"
        header-align="center"
        align="center"
        label="设备名称">
      </el-table-column>
      <el-table-column
        prop="username"
        header-align="center"
        align="center"
        label="操作人">
      </el-table-column>
      <el-table-column
        prop="createdTime"
        header-align="center"
        align="center"
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
          operationTime: ''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        options: this.getSelect(),
        pickerOptions: {
          shortcuts: [{
            text: '最近一周',
            onClick (picker) {
              const end = new Date()
              const start = new Date()
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 7)
              picker.$emit('pick', [start, end])
            }
          }, {
            text: '最近一个月',
            onClick (picker) {
              const end = new Date()
              const start = new Date()
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 30)
              picker.$emit('pick', [start, end])
            }
          }, {
            text: '最近三个月',
            onClick (picker) {
              const end = new Date()
              const start = new Date()
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 90)
              picker.$emit('pick', [start, end])
            }
          }]
        }
      }
    },
    components: {
      AddOrUpdate
    },
    activated () {
      this.getDataList()
    },
    methods: {
      getSelect () {
        this.dataListLoading = true
        let s = []
        this.$http({
          url: this.$http.adornUrl('/backstage/GET/operation-type'),
          method: 'get'
        }).then(({data}) => {
          if (data.status === 'success') {
            let typeArray = data.rows
            for (let i = 0; i < typeArray.length; i++) {
              let type = {}
              type.code = typeArray[i].code
              type.name = typeArray[i].name
              s.push(type)
            }
          } else {
            s = []
          }
          this.dataListLoading = false
        })
        return s
      },
      changeDate (val) {
        this.dataForm.operationTime = val
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
          url: this.$http.adornUrl('/backstage/GET/deviceOperationLogs'),
          method: 'post',
          params: this.$http.adornParams({
            'currentPage': this.pageIndex,
            'pageSize': this.pageSize,
            'operationTypeCode': this.dataForm.operationTypeCode,
            'deviceName': this.dataForm.deviceName,
            'username': this.dataForm.username,
            'startTime': this.dataForm.operationTime[0],
            'endTime': this.dataForm.operationTime[1]
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
