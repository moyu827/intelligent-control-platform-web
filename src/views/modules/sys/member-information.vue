<template>
  <div>
    <div style="width: 15%;float: left">
      <el-tree
        :data="data"
        default-expand-all
        node-key="id"
        ref="tree"
        @node-click="getCheckedKeys"
        highlight-current
        :props="defaultProps">
      </el-tree>
    </div>
    <div style="width: 85%;float: left">
      <el-form :inline="true" :model="dataForm" ref="dataForm" @keyup.enter.native="getDataList()">
        <el-form-item>
          成员名称
        </el-form-item>
        <el-form-item prop="username">
          <el-input v-model="dataForm.username" clearable placeholder="成员名称" ></el-input>
        </el-form-item>
        <el-form-item prop="orgCode" style="display: none">
          <el-input v-model="dataForm.orgCode" clearable></el-input>
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
          prop="userName"
          header-align="center"
          align="center"
          width="80"
          label="姓名">
        </el-table-column>
        <el-table-column
          prop="job"
          header-align="center"
          align="center"
          :formatter="formatterJob"
          label="职务">
        </el-table-column>
        <el-table-column
          prop="orgName"
          header-align="center"
          align="center"
          label="组织机构">
        </el-table-column>
        <el-table-column
          prop="mobile"
          header-align="center"
          align="center"
          label="手机号码">
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
  </div>
</template>
<script>
  import AddOrUpdate from './user-add-or-update'
  export default {
    data () {
      return {
        data: this.getTree(),
        defaultProps: {
          children: 'children',
          label: 'label'
        },
        dataForm: {
          operationTime: ''
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
      formatterJob: function (row, column, cellValue, index) {
        if (cellValue === '') {
          return '______'
        } else {
          return cellValue
        }
      },
      getCheckedKeys (data) {
        this.dataForm.orgCode = data.id
      },
      getTree () {
        this.dataListLoading = true
        let s = []
        this.$http({
          url: this.$http.adornUrl('/backstage/GET/web/sysOrgTree'),
          method: 'get'
        }).then(({data}) => {
          for (let i in data.rows) {
            s.push(data.rows[i])
          }
          console.log(s)
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
          url: this.$http.adornUrl('/backstage/GET/sysUsers'),
          method: 'post',
          params: this.$http.adornParams({
            'currentPage': this.pageIndex,
            'pageSize': this.pageSize,
            'username': this.dataForm.username,
            'orgCode': this.dataForm.orgCode
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
