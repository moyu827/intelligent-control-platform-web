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
          <el-form-item label="厂商名称" prop="name">
            <el-input v-model="dataForm.name" placeholder="厂商名称"></el-input>
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
            name: ''
          },
          dataRule: {
            name: [
                        { required: true, message: '厂商名称不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/sys/overhaul-unit/GET/overhaulUnits`),
              method: 'get',
              params: this.$http.adornParams({
                'id': id
              })
            }).then(({data}) => {
              if (data.status === 'success') {
                this.dataForm.id = data.rows[0].id
                this.dataForm.name = data.rows[0].name
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
                  url: this.$http.adornUrl(`/sys/overhaul-unit/PUT/overhaulUnit`),
                  method: 'put',
                  data: this.$http.adornData({
                    'id': id,
                    'name': this.dataForm.name
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
                  url: this.$http.adornUrl(`/sys/overhaul-unit/POST/overhaulUnit`),
                  method: 'post',
                  data: this.$http.adornData({
                    'name': this.dataForm.name
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
