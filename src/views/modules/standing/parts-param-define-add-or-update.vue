<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    width="35%"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="120px">
      <el-form-item style="display: none">
        <el-input v-model="dataForm.objId"></el-input>
      </el-form-item>
      <el-row>
        <el-col :span="24">
          <el-form-item label="设备类型" prop="sblx">
            <el-select  v-model="dataForm.sblx" :filter-method="sblxFilter"  style="width: 220px;" filterable     placeholder="请选择">
              <el-option
                v-for="item in sblxList"
                :key="item.sblxbm"
                :label="item.sblx"
                :value="item.sblxbm">
              </el-option>
            </el-select>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="24">
          <el-form-item label="参数名称" prop="csmc">
            <el-input v-model="dataForm.csmc" ></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="24">
          <el-form-item label="数据类型" prop="sjlx">
            <el-input v-model="dataForm.sjlx" ></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="24">
          <el-form-item label="计量单位" prop="jldw">
            <el-input v-model="dataForm.jldw" ></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="24">
          <el-form-item label="精度" prop="jd">
            <el-input v-model="dataForm.jd"  @keyup.native="number" ></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="24">
          <el-form-item label="字段长度" prop="zdcd">
            <el-input v-model="dataForm.zdcd" ></el-input>
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
            sblx: '',
            csmc: '',
            sjlx: '',
            jldw: '',
            jd: '',
            zdcd: ''
          },
          sblxList: [],
          allSblxList: [],
          dataRule: {
            // name: [
            //             { required: true, message: '电压等级名称不能为空', trigger: 'blur' }
            // ]
          }
        }
      },
      components: {
      },
      methods: {
        number () {
          this.dataForm.jd = this.dataForm.jd.replace(/[^\\.\d]/g, '')

          this.dataForm.jd = this.dataForm.jd.replace('.', '')
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
        init (id) {
          this.visible = true
          this.getsblx()
          this.dataForm.objId = id || 0
          if (id !== undefined && id !== 0) {
            this.$http({
              url: this.$http.adornUrl(`/standing/parts-param-define/GET/partsParamDefine`),
              method: 'get',
              params: this.$http.adornParams({
                'objId': id
              })
            }).then(({data}) => {
              if (data.status === 'success') {
                this.sblxFilter(data.rows[0].sblx)
                this.dataForm.objId = data.rows[0].objId
                this.dataForm.sblx = data.rows[0].sblx
                this.dataForm.csmc = data.rows[0].csmc
                this.dataForm.sjlx = data.rows[0].sjlx
                this.dataForm.jldw = data.rows[0].jldw
                this.dataForm.jd = data.rows[0].jd
                this.dataForm.zdcd = data.rows[0].zdcd
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
                  url: this.$http.adornUrl(`/standing/parts-param-define/PUT/partsParamDefine`),
                  method: 'put',
                  data: this.$http.adornData({
                    'objId': objId,
                    'sblx': this.dataForm.sblx,
                    'csmc': this.dataForm.csmc,
                    'sjlx': this.dataForm.sjlx,
                    'jldw': this.dataForm.jldw,
                    'jd': this.dataForm.jd,
                    'zdcd': this.dataForm.zdcd
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
                  url: this.$http.adornUrl(`/standing/parts-param-define/POST/partsParamDefine`),
                  method: 'post',
                  data: this.$http.adornData({
                    'sblx': this.dataForm.sblx,
                    'csmc': this.dataForm.csmc,
                    'sjlx': this.dataForm.sjlx,
                    'jldw': this.dataForm.jldw,
                    'jd': this.dataForm.jd,
                    'zdcd': this.dataForm.zdcd
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
