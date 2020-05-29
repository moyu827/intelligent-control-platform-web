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
          <el-form-item label="子控区类别" prop="type">
            <el-radio-group v-model="dataForm.type" @change="changeType">
              <el-radio :label= 1>省级</el-radio>
              <el-radio :label= 2>市级</el-radio>
              <el-radio :label= 3>县级</el-radio>
            </el-radio-group>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="24">
          <el-form-item label="子控区名称" prop="name">
            <el-input v-model="dataForm.name" placeholder="子控区名称"></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-form-item v-if="dataForm.type !== 1"  label="上级单位" prop="parentId" >
          <el-select  v-model="dataForm.parentId"  filterable    clearable placeholder="请选择">
            <el-option
              v-for="item in parentNames"
              :key="item.id"
              :label="item.name"
              :value="item.id">
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
            type: 1,
            name: '',
            parentId: ''
          },
          parentNames: [],
          dataRule: {
            name: [
                        { required: true, message: '电压等级名称不能为空', trigger: 'blur' }
            ]
          }
        }
      },
      components: {
      },
      methods: {
        changeType (val) {
          if (val === 2) {
            this.getParentAreaList(1)
          } else if (val === 3) {
            this.getParentAreaList(2)
          }
        },
        getParentAreaList (type) {
          this.$http({
            url: this.$http.adornUrl(`/bus/sub-control-area/GET/subControlAreas`),
            method: 'get',
            params: this.$http.adornParams({
              'type': type
            })
          }).then(({data}) => {
            if (data.status === 'success') {
              this.parentNames = data.rows
            } else {
              this.parentNames = []
            }
          })
        },
        init (id, type) {
          this.visible = true
          this.dataForm.id = id || 0
          if (id !== undefined && id !== 0) {
            if (type === 2) {
              this.getParentAreaList(1)
            } else if (type === 3) {
              this.getParentAreaList(2)
            }
            this.$http({
              url: this.$http.adornUrl(`/bus/sub-control-area/GET/subControlAreas`),
              method: 'get',
              params: this.$http.adornParams({
                'id': id
              })
            }).then(({data}) => {
              if (data.status === 'success') {
                this.dataForm.id = data.rows[0].id
                this.dataForm.name = data.rows[0].name
                this.dataForm.parentId = data.rows[0].parentId
                this.dataForm.type = data.rows[0].type
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
                  url: this.$http.adornUrl(`/bus/sub-control-area/PUT/subControlArea`),
                  method: 'put',
                  data: this.$http.adornData({
                    'id': id,
                    'name': this.dataForm.name,
                    'parentId': this.dataForm.parentId,
                    'type': this.dataForm.type
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
                  url: this.$http.adornUrl(`/bus/sub-control-area/POST/subControlArea`),
                  method: 'post',
                  data: this.$http.adornData({
                    'name': this.dataForm.name,
                    'parentId': this.dataForm.parentId,
                    'type': this.dataForm.type
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
