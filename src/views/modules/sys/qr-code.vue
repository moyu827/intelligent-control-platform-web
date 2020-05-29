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
        二维码状态
      </el-form-item>
      <el-form-item prop="state">
        <el-select v-model="dataForm.state" clearable placeholder="请选择">
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
      <el-form-item>
        <el-button type="primary" @click="addQrCode()">新增</el-button>
      </el-form-item>
      <el-form-item>
        <el-button type="down" @click="bulkDownload()">批量下载</el-button>
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column
        type="selection"
        width="55">
      </el-table-column>
      <el-table-column
        type="id"
        v-if="false"
        label="ID">
      </el-table-column>
      <el-table-column
        type="index"
        :index="indexMethod"
        width="60"
        align="center"
        label="序号">
      </el-table-column>
      <el-table-column
        prop="name"
        header-align="center"
        align="center"

        label="名称">
      </el-table-column>
      <el-table-column
        prop="state"
        header-align="center"
        align="center"
        :formatter="formatterState"
        label="状态">
      </el-table-column>
      <el-table-column
        prop="equipmentName"
        :show-overflow-tooltip="true"
        header-align="center"
        align="center"
        label="关联设备">
      </el-table-column>
      <el-table-column
        header-align="center"
        align="center"
        label="操作 ">
        <template slot-scope="scope">
          <el-button  @click="viewPicture(scope.row.viewPosition)" type="text" size="small">查看</el-button>
          <el-button  @click="downloadFile(scope.row.downloadPosition)" type="text" size="small">下载</el-button>
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
    <picture-list v-if="pictureListVisible" ref="pictureList"></picture-list>
  </div>
</template>
<style>
  body .el-table th.gutter{
    display: table-cell!important;
  }
</style>
<script>
  import PictureList from './device-picture'
  import JSZip from 'jszip'
  import FileSaver from 'file-saver'
  export default {
    data () {
      return {
        dataForm: {
        },
        isRouterAlive: true,
        options: [{
          value: '0',
          label: '未激活'
        }, {
          value: '1',
          label: '已激活'
        }, {
          value: '2',
          label: '已作废'
        }],
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        pictureListVisible: false
      }
    },
    components: {
      PictureList
    },
    activated () {
      this.getDataList()
    },
    methods: {
      getFile (url) {
        return this.$http({
          url: this.$http.adornUrl('/backstage/GET/download-file'),
          method: 'get',
          params: this.$http.adornParams({
            'filePath': url
          }),
          responseType: 'arraybuffer'
        })
      },
      bulkDownload () {
        const data = this.dataListSelections // 需要下载打包的路径, 可以是本地相对路径, 也可以是跨域的全路径
        const zip = new JSZip()
        const cache = {}
        const promises = []
        data.forEach(item => {
          const promise = this.getFile(item).then(data => {
            // console.log(new ArrayBuffer(data))
            // console.log(data)
           // 下载文件, 并存成ArrayBuffer对象
            const arrName = item.split('/')
            const fileName = arrName[arrName.length - 1] // 获取文件名
            zip.file(fileName, data.data, { binary: true }) // 逐个添加文件
            cache[fileName] = data
          })
          promises.push(promise)
        })
        Promise.all(promises).then(() => {
          zip.generateAsync({type: 'blob'}).then(content => { // 生成二进制流
            let url = window.URL.createObjectURL(content)
            let link = document.createElement('a')
            link.style.display = 'none'
            link.href = url
            // 获取文件名
            let fileName = '二维码.zip'
            link.setAttribute('download', fileName)
            document.body.appendChild(link)
            link.click()
            // FileSaver.saveAs(content, '打包下载.zip')// 利用file-saver保存文件
          })
        })
      },
      formatterState: function (row, column, cellValue, index) {
        switch (cellValue) {
          case 0:return '未激活'
          case 1:return '已激活'
          case 2:return '已废除'
        }
      },
      addQrCode () {
        this.$prompt('请输入要新增的数量', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          inputPattern: /(^[1-9]\d*$)/,
          inputErrorMessage: '请输入正整数'
        }).then(({ value }) => {
          this.dataListLoading = true
          this.$http({
            url: this.$http.adornUrl('/backstage/POST/qrcode'),
            method: 'post',
            params: this.$http.adornParams({
              'num': value
            })
          }).then(({data}) => {
            if (data.status === 'success') {
              this.$message({
                type: 'success',
                message: data.message
              })
            } else {
              this.$message({
                type: 'fail',
                message: data.message
              })
            }
            this.getDataList()
            this.dataListLoading = false
          })
        }).catch(() => {
        })
      },
      indexMethod (index) {
        return index + 1
      },
      clearDataForm () {
        this.$refs.dataForm.resetFields()
      },
      viewPicture (row) {
        let pictureList = []
        pictureList.push(row)
        this.pictureListVisible = true
        this.$nextTick(() => {
          this.$refs.pictureList.init(pictureList, '300px', '300px')
        })
      },
      downloadFile (row) {
        this.$http({
          url: this.$http.adornUrl('/backstage/GET/download-file'),
          method: 'get',
          params: this.$http.adornParams({
            'filePath': row
          }),
          responseType: 'blob'
        }).then(({data}) => {
          if (!data) {
            return
          }
          let url = window.URL.createObjectURL(new Blob([data]))
          let link = document.createElement('a')
          link.style.display = 'none'
          link.href = url
          // 获取文件名
          let fileName = null
          if (row.indexOf('/') !== -1) {
            fileName = row.substring(row.lastIndexOf('/') + 1)
          } else {
            fileName = row
          }
          // download 属性定义了下载链接的地址而不是跳转路径
          link.setAttribute('download', fileName)
          document.body.appendChild(link)
          link.click()
        })
      },
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.dataList = []
        this.$http({
          url: this.$http.adornUrl('/backstage/GET/qr-code-list'),
          method: 'get',
          params: this.$http.adornParams({
            'currentPage': this.pageIndex,
            'pageSize': this.pageSize,
            'equipmentName': this.dataForm.deviceName,
            'state': this.dataForm.state
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
        this.dataListSelections = []
        for (let i = 0; i < val.length; i++) {
          this.dataListSelections.push(val[i].downloadPosition)
        }
      }
    }
  }
</script>
