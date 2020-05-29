<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    width="720px"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item style="display: none" prop="id">
        <el-input v-model="dataForm.id"></el-input>
      </el-form-item>
      <el-row>
        <el-col :span="24">
          <el-form-item label="参数名称" prop="name">
            <el-input v-model="dataForm.name" placeholder="参数名称"></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="24">
          <el-form-item label="参数值" prop="value">
            <el-input v-model="dataForm.value" placeholder="参数值"></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-form-item style="display: none" prop="modelId">
        <el-input v-model="dataForm.modelId"></el-input>
      </el-form-item>
      <el-row>
        <el-col :span="24">
          <el-form-item label="设备型号" prop="modelName">
            <el-dropdown  trigger="click"  ref="ddlAddress" style="width: 600px">
               <el-input v-model="dataForm.modelName"  readonly placeholder="设备型号"></el-input>
              <el-dropdown-menu  slot="dropdown">
                <el-form :inline="true" :model="tableForm" @keyup.enter.native="getDataList()">
                  <el-form-item>
                    <el-input v-model="tableForm.name" placeholder="型号名称" clearable></el-input>
                  </el-form-item>
                  <el-form-item>
                    <el-button @click="getDataList()">查询</el-button>
                  </el-form-item>
                </el-form>
                <el-table
                  :data="tableData"
                  max-height="250"
                  @row-click="handleRowClicked">
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
                    width="180"
                    label="型号名称">
                  </el-table-column>
                  <el-table-column
                    prop="typeName"
                    header-align="center"
                    align="center"
                    width="180"
                    label="型号类型">
                  </el-table-column>
                  <el-table-column
                    prop="manufacturerName"
                    header-align="center"
                    align="center"
                    width="180"
                    label="厂商">
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
              </el-dropdown-menu>
            </el-dropdown>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="24">
          <el-form-item label="设备类型" prop="typeName">
            <el-input v-model="dataForm.typeName" disabled placeholder="设备类型"></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="24">
          <el-form-item label="设备厂商" prop="manufacturerName">
            <el-input v-model="dataForm.manufacturerName" disabled placeholder="厂商名称"></el-input>
          </el-form-item>
        </el-col>
      </el-row>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>

  </el-dialog>
</template>
<style>
  .el-dropdown-link {
    cursor: pointer;
    color: #409EFF;
  }
  .el-icon-arrow-down {
    font-size: 12px;
  }
</style>
<script>
    export default {
      data () {
        return {
          tableForm: {
            name: ''
          },
          visible: false,
          dataForm: {
            id: 0,
            name: '',
            value: '',
            modelId: '',
            modelName: '',
            typeName: '',
            manufacturerName: ''

          },
          pageIndex: 1,
          pageSize: 10,
          totalPage: 0,
          tableData: [],
          dataRule: {
            name: [
                        { required: true, message: '参数名称不能为空', trigger: 'blur' }
            ],
            value: [
                  { required: true, message: '参数值不能为空', trigger: 'blur' }
            ],
            modelName: [
                  { required: true, message: '设备型号不能为空', trigger: 'blur' }
            ]
          }
        }
      },
      methods: {
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
        handleRowClicked (row, column, event) {
          this.dataForm.modelId = row.id
          this.dataForm.modelName = row.name
          this.dataForm.typeName = row.typeName
          this.dataForm.manufacturerName = row.manufacturerName
          this.$refs.ddlAddress.hide()
        },
        getDataList () {
          this.$http({
            url: this.$http.adornUrl('/bus/power-dev-model/GET/powerDevModels'),
            method: 'get',
            params: this.$http.adornParams({
              'currentPage': this.pageIndex,
              'pageSize': this.pageSize,
              'name': this.tableForm.name
            })
          }).then(({data}) => {
            if (data.status === 'success') {
              this.tableData = data.rows
              this.totalPage = data.total
            } else {
              this.tableData = []
              this.totalPage = 10
            }
          })
        },
        init (id) {
          this.getDataList()
          this.visible = true
          this.dataForm.id = id || 0
          if (id !== undefined && id !== 0) {
            this.$http({
              url: this.$http.adornUrl(`/bus/power-dev-nameplate/GET/powerDevNameplates`),
              method: 'get',
              params: this.$http.adornParams({
                'id': id
              })
            }).then(({data}) => {
              if (data.status === 'success') {
                this.dataForm.id = data.rows[0].id
                this.dataForm.name = data.rows[0].name
                this.dataForm.value = data.rows[0].value
                this.dataForm.modelId = data.rows[0].modelId
                this.dataForm.modelName = data.rows[0].modelName
                this.dataForm.typeName = data.rows[0].typeName
                this.dataForm.manufacturerName = data.rows[0].manufacturerName
              } else {
                this.$message.error(data.message)
              }
            })
          } else {
            if (this.$refs['dataForm'] !== undefined) {
              this.$refs['dataForm'].resetFields()
            }
          }
        },
            // 表单提交
        dataFormSubmit () {
          this.$refs['dataForm'].validate((valid) => {
            if (valid) {
              let id = this.dataForm.id
              if (id !== undefined && id !== 0) {
                this.$http({
                  url: this.$http.adornUrl(`/bus/power-dev-nameplate/PUT/powerDevNameplate`),
                  method: 'put',
                  data: this.$http.adornData({
                    'id': id,
                    'name': this.dataForm.name,
                    'value': this.dataForm.value,
                    'modelId': this.dataForm.modelId,
                  })
                }).then(({data}) => {
                  if (data.status === 'success') {
                    this.$message({
                      message: '操作成功',
                      type: 'success',
                      duration: 1500,
                      onClose: () => {
                        this.visible = false
                        this.$emit('refreshDataList')
                      }
                    })
                  } else {
                    this.$message.error(data.message)
                  }
                })
              } else {
                this.$http({
                  url: this.$http.adornUrl(`/bus/power-dev-nameplate/POST/powerDevNameplate`),
                  method: 'post',
                  data: this.$http.adornData({
                    'name': this.dataForm.name,
                    'value': this.dataForm.value,
                    'modelId': this.dataForm.modelId
                  })
                }).then(({data}) => {
                  if (data.status === 'success') {
                    this.$message({
                      message: '操作成功',
                      type: 'success',
                      duration: 1500,
                      onClose: () => {
                        this.visible = false
                        this.$emit('refreshDataList')
                      }
                    })
                  } else {
                    this.$message.error(data.message)
                  }
                })
              }
            }
          })
        }
      }
    }
</script>
