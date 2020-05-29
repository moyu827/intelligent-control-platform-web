<template>
  <el-dialog
    :title="!dataForm.objId ? '新增' : '修改'"
    :close-on-click-modal="false"
    width="35%"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="120px">
      <el-form-item style="display: none">
        <el-input v-model="dataForm.objId"></el-input>
      </el-form-item>
      <el-row>
        <el-col :span="24">
          <el-form-item label="试验专业名称" prop="syzyid">
            <el-select  v-model="dataForm.syzyid"  style="width: 220px;" filterable     placeholder="请选择">
              <el-option
                v-for="item in syzyList"
                :key="item.objId"
                :label="item.mc"
                :value="item.objId">
              </el-option>
            </el-select>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="24">
          <el-form-item label="试验性质名称" prop="mc">
            <el-input v-model="dataForm.mc" placeholder="试验性质名称"></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="24">
          <el-form-item label="序号" prop="xh">
            <el-input v-model="dataForm.xh" onkeyup="this.value=this.value.replace(/[^\d.]/g,'');" placeholder="序号"></el-input>
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
            objId: 0,
            mc: '',
            xh: '',
            syzyid: ''
          },
          syzyList: [],
          dataRule: {
            mc: [
                        { required: true, message: '实验性质名称不能为空', trigger: 'blur' }
            ]
          }
        }
      },
      components: {
      },
      methods: {
        getsszy () {
          this.$http({
            url: this.$http.adornUrl(`/experiment/speciality/GET/speciality`),
            method: 'get',
            params: this.$http.adornParams({
            })
          }).then(({data}) => {
            if (data.status === 'success') {
              if (data.rows.length > 0) {
                this.syzyList = data.rows
              }
            } else {
              this.syzyList = []
            }
          })
        },
        init (objId) {
          this.getsszy()
          this.visible = true
          this.dataForm.objId = objId || 0
          if (objId !== undefined && objId !== 0) {
            this.$http({
              url: this.$http.adornUrl(`/experiment/nature/GET/nature`),
              method: 'get',
              params: this.$http.adornParams({
                'objId': objId
              })
            }).then(({data}) => {
              if (data.status === 'success') {
                this.dataForm.objId = data.rows[0].objId
                this.dataForm.mc = data.rows[0].mc
                this.dataForm.xh = data.rows[0].xh
                this.dataForm.syzyid = data.rows[0].syzyid
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
              let objId = this.dataForm.objId
              if (objId !== undefined && objId !== 0) {
                this.$http({
                  url: this.$http.adornUrl(`/experiment/nature/PUT/nature`),
                  method: 'put',
                  data: this.$http.adornData({
                    'objId': objId,
                    'mc': this.dataForm.mc,
                    'xh': this.dataForm.xh,
                    'syzyid': this.dataForm.syzyid
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
                  url: this.$http.adornUrl(`/experiment/nature/POST/nature`),
                  method: 'post',
                  data: this.$http.adornData({
                    'mc': this.dataForm.mc,
                    'xh': this.dataForm.xh,
                    'syzyid': this.dataForm.syzyid
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
