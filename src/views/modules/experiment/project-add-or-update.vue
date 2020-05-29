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
        <el-col :span="12">
          <el-form-item label="试验专业名称" prop="syzyid">
            <el-select  v-model="dataForm.syzyid"  filterable     placeholder="请选择">
              <el-option
                v-for="item in syzyList"
                :key="item.objId"
                :label="item.mc"
                :value="item.objId">
              </el-option>
            </el-select>

          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="试验项目名称" prop="mc">
            <el-input v-model="dataForm.mc" ></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="试验项目描述" prop="xmms">
            <el-input v-model="dataForm.xmms" ></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="显示方式" prop="xsfs">
            <el-input v-model="dataForm.xsfs" ></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="子项目定位方式" prop="zxmdwfs">
            <el-input v-model="dataForm.zxmdwfs" ></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="是否录入温度" prop="sflrwd">
            <el-radio-group v-model="dataForm.sflrwd">
              <el-radio :label=1>是</el-radio>
              <el-radio :label=0>否</el-radio>
            </el-radio-group>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="是否录入湿度" prop="sflrsd">
            <el-radio-group v-model="dataForm.sflrsd">
              <el-radio :label=1>是</el-radio>
              <el-radio :label=0>否</el-radio>
            </el-radio-group>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="是否录入油温" prop="sflryw">
            <el-radio-group v-model="dataForm.sflryw">
              <el-radio :label=1>是</el-radio>
              <el-radio :label=0>否</el-radio>
            </el-radio-group>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="是否录入负荷" prop="sflrfh">
            <el-radio-group v-model="dataForm.sflrfh">
              <el-radio :label=1>是</el-radio>
              <el-radio :label=0>否</el-radio>
            </el-radio-group>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="是否部位可扩展" prop="sfbwkkz">
            <el-radio-group v-model="dataForm.sfbwkkz">
              <el-radio :label=1>是</el-radio>
              <el-radio :label=0>否</el-radio>
            </el-radio-group>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="编码" prop="bm">
            <el-input v-model="dataForm.bm" ></el-input>
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
            syzyid: '',
            mc: '',
            xmms: '',
            xsfs: '',
            zxmdwfs: '',
            sflrwd: '',
            sflrsd: '',
            sflryw: '',
            sflrfh: '',
            sfbwkkz: '',
            bm: ''

          },
          syzyList: [],
          dataRule: {
            // mc: [
            //             { required: true, message: '试验专业名称不能为空', trigger: 'blur' }
            // ]
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
              url: this.$http.adornUrl(`/experiment/speciality/GET/speciality`),
              method: 'get',
              params: this.$http.adornParams({
                'objId': objId
              })
            }).then(({data}) => {
              if (data.status === 'success') {
                this.dataForm.objId = data.rows[0].objId
                this.dataForm.syzyid = data.rows[0].syzyid
                this.dataForm.mc = data.rows[0].mc
                this.dataForm.xmms = data.rows[0].xmms
                this.dataForm.xsfs = data.rows[0].xsfs
                this.dataForm.zxmdwfs = data.rows[0].zxmdwfs
                this.dataForm.sflrwd = data.rows[0].sflrwd
                this.dataForm.sflrsd = data.rows[0].sflrsd
                this.dataForm.sflryw = data.rows[0].sflryw
                this.dataForm.sflrfh = data.rows[0].sflrfh
                this.dataForm.sfbwkkz = data.rows[0].sfbwkkz
                this.dataForm.bm = data.rows[0].bm
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
                  url: this.$http.adornUrl(`/experiment/speciality/PUT/speciality`),
                  method: 'put',
                  data: this.$http.adornData({
                    'objId': objId,
                    'mc': this.dataForm.mc,
                    'syzyid': this.dataForm.syzyid,
                    'xmms': this.dataForm.xmms,
                    'xsfs': this.dataForm.xsfs,
                    'zxmdwfs': this.dataForm.zxmdwfs,
                    'sflrwd': this.dataForm.sflrwd,
                    'sflrsd': this.dataForm.sflrsd,
                    'sflryw': this.dataForm.sflryw,
                    'sflrfh': this.dataForm.sflrfh,
                    'sfbwkkz': this.dataForm.sfbwkkz,
                    'bm': this.dataForm.bm

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
                  url: this.$http.adornUrl(`/experiment/speciality/POST/speciality`),
                  method: 'post',
                  data: this.$http.adornData({
                    'mc': this.dataForm.mc,
                    'syzyid': this.dataForm.syzyid,
                    'xmms': this.dataForm.xmms,
                    'xsfs': this.dataForm.xsfs,
                    'zxmdwfs': this.dataForm.zxmdwfs,
                    'sflrwd': this.dataForm.sflrwd,
                    'sflrsd': this.dataForm.sflrsd,
                    'sflryw': this.dataForm.sflryw,
                    'sflrfh': this.dataForm.sflrfh,
                    'sfbwkkz': this.dataForm.sfbwkkz,
                    'bm': this.dataForm.bm
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
