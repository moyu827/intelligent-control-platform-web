<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    width="35%"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="120px">
      <el-form-item style="display: none">
        <el-input v-model="dataForm.id"></el-input>
      </el-form-item>
      <el-row>
        <el-col :span="24">
          <el-form-item label="变电站名称" prop="name">
            <el-input v-model="dataForm.name" placeholder="变电站名称"></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="24">
          <el-form-item label="电压等级" prop="voltageId">
            <el-select v-model="dataForm.voltageId" filterable placeholder="请选择">
              <el-option
                v-for="item in voltageList"
                :key="item.id"
                :label="item.name"
                :value="item.id">
              </el-option>
            </el-select>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="24">
          <el-form-item label="所属子控区" prop="areaId">
            <el-select v-model="dataForm.areaId"  filterable placeholder="请选择">
              <el-option
                v-for="item in controlAreaList"
                :key="item.id"
                :label="item.name"
                :value="item.id">
              </el-option>
            </el-select>
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

<script>
    export default {
      data () {
        return {
          visible: false,
          dataForm: {
            id: 0,
            name: '',
            voltageId: '',
            areaId: ''
          },
          voltageList: [],
          controlAreaList: [],
          dataRule: {
            name: [
                        { required: true, message: '电压等级名称不能为空', trigger: 'blur' }
            ]
          }
        }
      },
      activated () {
        // this.getControlAreaList()
        // this.getVoltageList()
      },
      components: {
      },
      methods: {
        getVoltageList () {
          this.$http({
            url: this.$http.adornUrl(`/bus/voltage-level/GET/voltageLevels`),
            method: 'get'
          }).then(({data}) => {
            if (data.status === 'success') {
              this.voltageList = data.rows
            } else {
              this.voltageList = []
            }
          })
        },
        getControlAreaList () {
          this.$http({
            url: this.$http.adornUrl(`/bus/sub-control-area/GET/subControlAreas`),
            method: 'get'
          }).then(({data}) => {
            if (data.status === 'success') {
              this.controlAreaList = data.rows
            } else {
              this.controlAreaList = []
            }
          })
        },
        init (id) {
          this.getControlAreaList()
          this.getVoltageList()
          this.visible = true
          this.dataForm.id = id || 0
          if (id !== undefined && id !== 0) {
            this.$http({
              url: this.$http.adornUrl(`/bus/sub-station/GET/subStations`),
              method: 'get',
              params: this.$http.adornParams({
                'id': id
              })
            }).then(({data}) => {
              if (data.status === 'success') {
                this.dataForm.id = data.rows[0].id
                this.dataForm.name = data.rows[0].name
                this.dataForm.voltageId = data.rows[0].voltageId
                this.dataForm.areaId = data.rows[0].areaId
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
                  url: this.$http.adornUrl(`/bus/sub-station/PUT/subStation`),
                  method: 'put',
                  data: this.$http.adornData({
                    'id': id,
                    'name': this.dataForm.name,
                    'voltageId': this.dataForm.voltageId,
                    'areaId': this.dataForm.areaId
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
                  url: this.$http.adornUrl(`/bus/sub-station/POST/subStation`),
                  method: 'post',
                  data: this.$http.adornData({
                    'name': this.dataForm.name,
                    'voltageId': this.dataForm.voltageId,
                    'areaId': this.dataForm.areaId
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
