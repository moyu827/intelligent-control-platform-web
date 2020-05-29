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
            <el-select  v-model="dataForm.sblx" :filter-method="sblxFilter"  style="width: 220px;" filterable      placeholder="请选择">
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
          <el-form-item label="部件" prop="bjid">
            <el-select  v-model="dataForm.bjid" :filter-method="bjFilter"  style="width: 220px;" filterable      placeholder="请选择">
              <el-option
                v-for="item in bjList"
                :key="item.objId"
                :label="item.sbmc"
                :value="item.objId">
              </el-option>
            </el-select>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="24">
          <el-form-item label="参数" prop="csdyid">
            <el-select  v-model="dataForm.csdyid" :filter-method="csFilter"  style="width: 220px;" filterable @change="changeCs"      placeholder="请选择">
              <el-option
                v-for="item in csList"
                :key="item.objId"
                :label="item.csmc"
                :value="item.objId">
              </el-option>
            </el-select>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="24">
          <el-form-item label="参数值" prop="csz">
            <el-input v-model="dataForm.csz" ></el-input>
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
            bjid: '',
            csdyid: '',
            csmc: '',
            csz: ''
          },
          bjList: [],
          allBjList: [],
          csList: [],
          allCsList: [],
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
        changeCs (val) {
          let csList = this.csList
          if (csList.length > 0) {
            for (const i in this.csList) {
              if (csList[i].objId === val) {
                this.dataForm.csmc = csList[i].csmc
              }
            }
          }
        },
        bjFilter (query = '') {
          let arr = this.allBjList.filter((item) => {
            return item.objId.includes(query) || item.sbmc.includes(query)
          })
          if (arr.length > 50) {
            this.bjList = arr.slice(0, 50)
          } else {
            this.bjList = arr
          }
        },
        getbj () {
          this.$http({
            url: this.$http.adornUrl(`/select/GET/selectPartsAccessories`),
            method: 'get',
            params: this.$http.adornParams({
            })
          }).then(({data}) => {
            if (data.status === 'success') {
              if (data.rows.length > 0) {
                this.allBjList = data.rows
                this.bjFilter()
              }
            } else {
              this.allBjList = []
            }
          })
        },
        csFilter (query = '') {
          let arr = this.allCsList.filter((item) => {
            return item.objId.includes(query) || item.csmc.includes(query)
          })
          if (arr.length > 50) {
            this.csList = arr.slice(0, 50)
          } else {
            this.csList = arr
          }
        },
        getcs () {
          this.$http({
            url: this.$http.adornUrl(`/select/GET/selectPartsParamDefine`),
            method: 'get',
            params: this.$http.adornParams({
            })
          }).then(({data}) => {
            if (data.status === 'success') {
              if (data.rows.length > 0) {
                this.allCsList = data.rows
                this.csFilter()
              }
            } else {
              this.allCsList = []
            }
          })
        },
        sblxFilter (query = '') {
          let arr = this.allSblxList.filter((item) => {
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
          this.getbj()
          this.getcs()
          this.dataForm.objId = id || 0
          if (id !== undefined && id !== 0) {
            this.$http({
              url: this.$http.adornUrl(`/standing/parts-param/GET/partsParam`),
              method: 'get',
              params: this.$http.adornParams({
                'objId': id
              })
            }).then(({data}) => {
              if (data.status === 'success') {
                this.sblxFilter(data.rows[0].sblx)
                this.bjFilter(data.rows[0].bjid)
                this.csFilter(data.rows[0].csdyid)
                this.dataForm.objId = data.rows[0].objId
                this.dataForm.sblx = data.rows[0].sblx
                this.dataForm.bjid = data.rows[0].bjid
                this.dataForm.csdyid = data.rows[0].csdyid
                this.dataForm.csmc = data.rows[0].csmc
                this.dataForm.csz = data.rows[0].csz
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
                  url: this.$http.adornUrl(`/standing/parts-param/PUT/partsParam`),
                  method: 'put',
                  data: this.$http.adornData({
                    'objId': objId,
                    'sblx': this.dataForm.sblx,
                    'bjid': this.dataForm.bjid,
                    'csdyid': this.dataForm.csdyid,
                    'csmc': this.dataForm.csmc,
                    'csz': this.dataForm.csz
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
                  url: this.$http.adornUrl(`/standing/parts-param/POST/partsParam`),
                  method: 'post',
                  data: this.$http.adornData({
                    'sblx': this.dataForm.sblx,
                    'bjid': this.dataForm.bjid,
                    'csdyid': this.dataForm.csdyid,
                    'csmc': this.dataForm.csmc,
                    'csz': this.dataForm.csz
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
