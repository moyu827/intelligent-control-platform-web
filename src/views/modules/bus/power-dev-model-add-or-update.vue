<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    width="30%"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item style="display: none">
        <el-input v-model="dataForm.id"></el-input>
      </el-form-item>
      <el-row>
        <el-col :span="24">
          <el-form-item label="型号名称" prop="name" >
            <el-input v-model="dataForm.name" placeholder="型号名称"></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-form-item label="设备类型" prop="typeId">
          <el-select  v-model="dataForm.typeId" :filter-method="sblxFilter"  style="width: 220px;" filterable    placeholder="请选择">
            <el-option
              v-for="item in sblxList"
              :key="item.sblxbm"
              :label="item.sblx"
              :value="item.sblxbm">
            </el-option>
          </el-select>
        </el-form-item>
      </el-row>
      <el-row>
        <el-form-item  label="厂商" prop="manufacturerId" >
          <el-select  v-model="dataForm.manufacturerId"  :filter-method="sccjFilter" style="width: 220px;" filterable     placeholder="请选择">
            <el-option
              v-for="item in sccjList"
              :key="item.sccjbm"
              :label="item.sccjmc"
              :value="item.sccjbm">
            </el-option>
          </el-select>
        </el-form-item>
      </el-row>

      <el-row>
        <el-form-item  label="试验模板" prop="testTemplateId" >
          <el-select  v-model="dataForm.testTemplateId"  :filter-method="symbFilter" style="width: 220px;" filterable     placeholder="请选择">
            <el-option
              v-for="item in symbList"
              :key="item.objId"
              :label="item.mc"
              :value="item.objId">
            </el-option>
          </el-select>
        </el-form-item>
      </el-row>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
    export default {
      data () {
        return {
          visible: false,
          dataForm: {
            id: 0,
            name: '',
            typeId: '',
            manufacturerId: '',
            testTemplateId: ''
          },
          sblxList: [],
          allSblxList: [],
          sccjList: [],
          allSccjList: [],
          symbList: [],
          allSymbList: [],
          types: [],
          manufacturers: [],
          value: '',
          dataRule: {
            name: [
                  { required: true, message: '设备型号名称不能为空', trigger: 'blur' }
            ],
            typeId: [
              {
                required: true, message: '设备型号类型不能为空', trigger: 'blur'
              }
            ],
            manufacturerId: [
              {
                required: true, message: '设备厂商不能为空', trigger: 'blur'
              }
            ]
          }
        }
      },
      activated () {
      },
      components: {
      },
      methods: {
        sccjFilter (query = '') {
          let arr = this.allSccjList.filter((item) => {
                  // return (item.sccjbm !== undefined && item.sccjbm !== '' && item.sccjbm.includes(query)) || (item.sccjmc !== undefined && item.sccjmc !== '' && item.sccjmc.includes(query))
            return item.sccjbm.includes(query) || item.sccjmc.includes(query)
          })
          if (arr.length > 50) {
            this.sccjList = arr.slice(0, 50)
          } else {
            this.sccjList = arr
          }
        },
        sblxFilter (query = '') {
          let arr = this.allSblxList.filter((item) => {
                  // return item !== null && ((item.sblxbm !== undefined && item.sblxbm !== '' && item.sblxbm.includes(query)) || (item.sblx !== undefined && item.sblx !== '' && item.sblx.includes(query)))
            return item.sblxbm.includes(query) || item.sblx.includes(query)
          })
          if (arr.length > 50) {
            this.sblxList = arr.slice(0, 50)
          } else {
            this.sblxList = arr
          }
        },
        symbFilter (query = '') {
          let arr = this.allSymbList.filter((item) => {
                  // return item !== null && ((item.sblxbm !== undefined && item.sblxbm !== '' && item.sblxbm.includes(query)) || (item.sblx !== undefined && item.sblx !== '' && item.sblx.includes(query)))
            return item.objId.includes(query) || item.mc.includes(query)
          })
          if (arr.length > 50) {
            this.symbList = arr.slice(0, 50)
          } else {
            this.symbList = arr
          }
        },

        getsblx () {
          this.$http({
            url: this.$http.adornUrl(`/select/GET/selectPortsType`),
            method: 'get',
            params: this.$http.adornParams({
            })
          }).then(({data}) => {
            if (data.status === 'success') {
              if (data.rows.length > 0) {
                this.allSblxList = data.rows
                this.sblxFilter()
              }
            } else {
              this.allSblxList = []
            }
          })
        },
        getsccj () {
          this.$http({
            url: this.$http.adornUrl(`/select/GET/selectMfrs`),
            method: 'get',
            params: this.$http.adornParams({
            })
          }).then(({data}) => {
            if (data.status === 'success') {
              if (data.rows.length > 0) {
                this.allSccjList = data.rows
                this.sccjFilter()
              }
            } else {
              this.allSccjList = []
            }
          })
        },
        getsymb () {
          this.$http({
            url: this.$http.adornUrl(`/select/GET/selectExperimentTemp`),
            method: 'get',
            params: this.$http.adornParams({
            })
          }).then(({data}) => {
            if (data.status === 'success') {
              if (data.rows.length > 0) {
                this.allSymbList = data.rows
                this.symbFilter()
              }
            } else {
              this.allSymbList = []
            }
          })
        },
        init (id) {
          this.visible = true
          this.getsblx()
          this.getsccj()
          this.getsymb()
          this.dataForm.id = id || 0
          if (id !== undefined && id !== 0) {
            this.$http({
              url: this.$http.adornUrl(`/bus/power-dev-model/GET/powerDevModels`),
              method: 'get',
              params: this.$http.adornParams({
                'id': id
              })
            }).then(({data}) => {
              if (data.status === 'success') {
                this.sblxFilter(data.rows[0].typeId)
                this.sccjFilter(data.rows[0].manufacturerId)
                this.symbFilter(data.rows[0].testTemplateId)
                this.dataForm.id = data.rows[0].id
                this.dataForm.name = data.rows[0].name
                this.dataForm.typeId = data.rows[0].typeId
                this.dataForm.manufacturerId = data.rows[0].manufacturerId
                this.dataForm.testTemplateId = data.rows[0].testTemplateId
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
          // 获取设备类型
        getPowerDevType () {
          this.$http({
            url: this.$http.adornUrl('/bus/power-dev-type/GET/powerDevTypes'),
            method: 'get',
            params: this.$http.adornParams({
            })
          }).then(({data}) => {
            if (data.status === 'success') {
              this.types = data.rows
            } else {
              this.types = []
            }
          })
        },
          // 获取设备厂商
        getPowerDevManufacturer () {
          this.$http({
            url: this.$http.adornUrl('/bus/power-dev-manufacturer/GET/powerDevManufacturers'),
            method: 'get',
            params: this.$http.adornParams({
            })
          }).then(({data}) => {
            if (data.status === 'success') {
              this.manufacturers = data.rows
            } else {
              this.manufacturers = []
            }
          })
        },
          // 表单提交
        dataFormSubmit () {
          this.$refs['dataForm'].validate((valid) => {
            if (valid) {
              let id = this.dataForm.id
              if (id !== undefined && id !== 0) {
                this.$http({
                  url: this.$http.adornUrl(`/bus/power-dev-model/PUT/powerDevModel`),
                  method: 'put',
                  data: this.$http.adornData({
                    'id': id,
                    'name': this.dataForm.name,
                    'typeId': this.dataForm.typeId,
                    'manufacturerId': this.dataForm.manufacturerId,
                    'testTemplateId': this.dataForm.testTemplateId
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
                  url: this.$http.adornUrl(`/bus/power-dev-model/POST/powerDevModel`),
                  method: 'post',
                  data: this.$http.adornData({
                    'name': this.dataForm.name,
                    'typeId': this.dataForm.typeId,
                    'manufacturerId': this.dataForm.manufacturerId,
                    'testTemplateId': this.dataForm.testTemplateId
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
