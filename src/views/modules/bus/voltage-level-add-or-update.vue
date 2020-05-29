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
          <el-form-item label="电压等级名称" prop="name">
            <el-input v-model="dataForm.name" placeholder="电压等级名称"></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="24">
          <el-form-item label="电压基准值" prop="value">
            <el-input v-model="dataForm.value" onkeyup="this.value=this.value.replace(/[^\d.]/g,'');" placeholder="电压基准值"></el-input>
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
            value: ''
          },
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
        init (id) {
          this.visible = true
          this.dataForm.id = id || 0
          if (id !== undefined && id !== 0) {
            this.$http({
              url: this.$http.adornUrl(`/bus/voltage-level/GET/voltageLevels`),
              method: 'get',
              params: this.$http.adornParams({
                'id': id
              })
            }).then(({data}) => {
              if (data.status === 'success') {
                this.dataForm.id = data.rows[0].id
                this.dataForm.name = data.rows[0].name
                this.dataForm.value = data.rows[0].value
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
                  url: this.$http.adornUrl(`/bus/voltage-level/PUT/voltageLevel`),
                  method: 'put',
                  data: this.$http.adornData({
                    'id': id,
                    'name': this.dataForm.name,
                    'value': this.dataForm.value
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
                  url: this.$http.adornUrl(`/bus/voltage-level/POST/voltageLevel`),
                  method: 'post',
                  data: this.$http.adornData({
                    'name': this.dataForm.name,
                    'value': this.dataForm.value
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
