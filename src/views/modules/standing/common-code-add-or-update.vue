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
          <el-form-item label="代码" prop="dm">
            <el-input v-model="dataForm.dm" ></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="24">
          <el-form-item label="代码名称" prop="dmmc">
            <el-input v-model="dataForm.dmmc" ></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="24">
          <el-form-item label="标准分类" prop="bzflid">
            <el-select  v-model="dataForm.bzflid" :filter-method="sjflFilter" @change="changeBzfl" style="width: 220px;" filterable  placeholder="请选择">
              <el-option
                v-for="item in bzflList"
                :key="item.objId"
                :label="item.bzflmc"
                :value="item.objId">
              </el-option>
            </el-select>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="24">
          <el-form-item label="备注" prop="bz">
            <el-input v-model="dataForm.bz" type="textarea"  resize="none" ></el-input>
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
            dm: '',
            dmmc: '',
            bzflbm: '',
            bzflid: '',
            bz: ''
          },
          bzflList: [],
          allBzflList: [],
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
        changeBzfl (val) {
          let bzflList = this.bzflList
          if (bzflList.length > 0) {
            for (const i in this.bzflList) {
              if (bzflList[i].objId === val) {
                this.dataForm.bzflbm = bzflList[i].bzflbm
              }
            }
          }
        },
        sjflFilter (query = '') {
          let arr = this.allBzflList.filter((item) => {
                  // return item !== null && ((item.sblxbm !== undefined && item.sblxbm !== '' && item.sblxbm.includes(query)) || (item.sblx !== undefined && item.sblx !== '' && item.sblx.includes(query)))
            return item.objId.includes(query) || item.bzflmc.includes(query)
          })
          if (arr.length > 50) {
            this.bzflList = arr.slice(0, 50)
          } else {
            this.bzflList = arr
          }
        },

        getSjfl () {
          this.$http({
            url: this.$http.adornUrl(`/select/GET/selectNormalSort`),
            method: 'get',
            params: this.$http.adornParams({
            })
          }).then(({data}) => {
            if (data.status === 'success') {
              if (data.rows.length > 0) {
                this.allBzflList = data.rows
                this.sjflFilter()
              }
            } else {
              this.allBzflList = []
            }
          })
        },
        init (id) {
          this.visible = true
          this.getSjfl()
          this.dataForm.id = id || 0
          if (id !== undefined && id !== 0) {
            this.$http({
              url: this.$http.adornUrl(`/standing/common-code/GET/commonCode`),
              method: 'get',
              params: this.$http.adornParams({
                'id': id
              })
            }).then(({data}) => {
              if (data.status === 'success') {
                this.sjflFilter(data.rows[0].bzflid)
                this.dataForm.objId = data.rows[0].objId
                this.dataForm.dm = data.rows[0].dm
                this.dataForm.dmmc = data.rows[0].dmmc
                this.dataForm.bzflbm = data.rows[0].bzflbm
                this.dataForm.bzflid = data.rows[0].bzflid
                this.dataForm.bz = data.rows[0].bz
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
                  url: this.$http.adornUrl(`/standing/common-code/PUT/commonCode`),
                  method: 'put',
                  data: this.$http.adornData({
                    'objId': objId,
                    'dm': this.dataForm.dm,
                    'dmmc': this.dataForm.dmmc,
                    'bzflbm': this.dataForm.bzflbm,
                    'bzflid': this.dataForm.bzflid,
                    'bz': this.dataForm.bz
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
                  url: this.$http.adornUrl(`/standing/common-code/POST/commonCode`),
                  method: 'post',
                  data: this.$http.adornData({
                    'dm': this.dataForm.dm,
                    'dmmc': this.dataForm.dmmc,
                    'bzflbm': this.dataForm.bzflbm,
                    'bzflid': this.dataForm.bzflid,
                    'bz': this.dataForm.bz
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
