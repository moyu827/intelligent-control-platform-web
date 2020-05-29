<template>
  <div>
    <el-form :inline="true" :model="dataForm" ref="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        设备名称
      </el-form-item>
      <el-form-item prop="deviceName">
        <el-input v-model="dataForm.deviceName" clearable placeholder="设备名称" ></el-input>
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
        label="设备名称">
      </el-table-column>
      <el-table-column
        prop="code"
        header-align="center"
        align="center"
        label="设备编号">
      </el-table-column>
      <el-table-column
        prop="model"
        :show-overflow-tooltip="true"
        header-align="center"
        align="center"
        label="规格型号">
      </el-table-column>
      <el-table-column
        prop="manufacturer"
        header-align="center"
        align="center"
        label="生产厂家">
      </el-table-column>
      <el-table-column
        prop="org"
        header-align="center"
        align="center"
        label="所属组织">
      </el-table-column>
      <el-table-column
        prop="contactor"
        header-align="center"
        align="center"
        label="责任人">
      </el-table-column>
      <el-table-column
        prop="location"
        header-align="center"
        align="center"
        label="安装位置">
      </el-table-column>
      <el-table-column
        header-align="center"
        align="center"
        label="图片">
        <template slot-scope="scope">
          <el-button @click="viewPicture(scope.row.pictureList)" type="text" size="small">查看图片</el-button>
        </template>
      </el-table-column>
      <el-table-column
        header-align="center"
        align="center"
        label="附件">
        <template slot-scope="scope">
          <a class='link-type' href='javascript:void(0)' @click="downloadFile(scope.row.attachmentList)">下载附件</a>
          <!--<el-button @click="downloadFile(scope.row.attachmentList)" type="text" size="small">下载附件</el-button>-->
        </template>
      </el-table-column>
      <el-table-column
        header-align="center"
        align="center"
        label="操作明细 ">
        <template slot-scope="scope">
          <el-button @click="viewEquipment(scope.row.id)" type="text" size="small">查看详情</el-button>
        </template>
      </el-table-column>
      <el-table-column
        header-align="center"
        align="center"
        label="操作 ">
        <template slot-scope="scope">
          <el-button @click="addOrUpdateHandle(scope.row.id)" type="text" size="small">编辑</el-button>
          <el-button @click="viewEquipment(scope.row.id)" type="text" size="small">删除</el-button>
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
    <picture-list v-if="pictureListVisible" ref="pictureList"></picture-list>
  </div>
</template>
<style>
  body .el-table th.gutter{
    display: table-cell!important;
  }
</style>
<script>
  import AddOrUpdate from './user-add-or-update'
  import PictureList from './device-picture'
  export default {
    data () {
      return {
        isRouterAlive: true,
        dataForm: {
          operationTime: ''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        pictureListVisible: false,
        addOrUpdateVisible: false
      }
    },
    components: {
      AddOrUpdate,
      PictureList
    },
    activated () {
      this.getDataList()
    },
    methods: {
      reload () {
        window.location.reload()
      },

      viewEquipment (id) {
        console.log(id)
      },
      viewPicture (row) {
        let length = row.length
        if (length === 0) {
          this.$message('该设备没有相关的图片')
          return
        }
        this.pictureListVisible = true
        this.$nextTick(() => {
          this.$refs.pictureList.init(row, '500px', '700px')
        })
      },
      downloadFile (row) {
        let length = row.length
        if (length === 0) {
          this.$message('该设备没有相关的附件')
          return
        }
        this.$http({
          url: this.$http.adornUrl('/backstage/GET/download-file'),
          method: 'get',
          params: this.$http.adornParams({
            'filePath': row[0]
          }),
          responseType: 'blob'
        }).then(({data}) => {
          console.log(data)
          if (!data) {
            return
          }

          let url = window.URL.createObjectURL(new Blob([data]))
          let link = document.createElement('a')
          link.style.display = 'none'
          link.href = url
          // 获取文件名
          let fileName = null
          if (row[0].indexOf('/') !== -1) {
            fileName = row[0].substring(row[0].lastIndexOf('/') + 1)
          } else {
            fileName = row[0]
          }
          // download 属性定义了下载链接的地址而不是跳转路径
          link.setAttribute('download', fileName)
          document.body.appendChild(link)
          link.click()
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
          url: this.$http.adornUrl('/backstage/GET/device-equipments'),
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
