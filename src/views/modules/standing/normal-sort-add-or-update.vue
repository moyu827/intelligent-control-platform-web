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
          <el-form-item label="标准分类编码" prop="bzflbm">
            <el-input v-model="dataForm.bzflbm" ></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="24">
          <el-form-item label="标准分类名称" prop="bzflmc">
            <el-input v-model="dataForm.bzflmc" ></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="24">
          <el-form-item label="上级分类" prop="sjflid">
            <el-select  v-model="dataForm.sjflid" :filter-method="sjflFilter"  style="width: 220px;" filterable  placeholder="请选择">
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
          <el-form-item label="是否国网标准" prop="sfgwbz">
            <el-radio-group v-model="dataForm.sfgwbz">
              <el-radio :label= 1>是</el-radio>
              <el-radio :label= 0>否</el-radio>
            </el-radio-group>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="24">
          <el-form-item label="国网标准代码" prop="gwbzdm">
            <el-input v-model="dataForm.gwbzdm" ></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="24">
          <el-form-item label="备注" prop="bz">
            <el-input type="textarea"  resize="none" v-model="dataForm.bz" ></el-input>
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
            bzflbm: '',
            bzflmc: '',
            sjflid: '',
            sfgwbz: 1,
            gwbzdm: '',
            bz: ''
          },
          bzflList: [],
          allBzflList: [],
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
          this.dataForm.objId = id || 0
          if (id !== undefined && id !== 0) {
            this.$http({
              url: this.$http.adornUrl(`/standing/normal-sort/GET/normalSort`),
              method: 'get',
              params: this.$http.adornParams({
                'objId': id
              })
            }).then(({data}) => {
              if (data.status === 'success') {
                this.sjflFilter(data.rows[0].sjflid)
                this.dataForm.objId = data.rows[0].objId
                this.dataForm.bzflbm = data.rows[0].bzflbm
                this.dataForm.bzflmc = data.rows[0].bzflmc
                this.dataForm.sjflid = data.rows[0].sjflid
                this.dataForm.sfgwbz = data.rows[0].sfgwbz
                this.dataForm.gwbzdm = data.rows[0].gwbzdm
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
                  url: this.$http.adornUrl(`/standing/normal-sort/PUT/normalSort`),
                  method: 'put',
                  data: this.$http.adornData({
                    'objId': objId,
                    'bzflbm': this.dataForm.bzflbm,
                    'bzflmc': this.dataForm.bzflmc,
                    'sjflid': this.dataForm.sjflid,
                    'sfgwbz': this.dataForm.sfgwbz,
                    'gwbzdm': this.dataForm.gwbzdm,
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
                  url: this.$http.adornUrl(`/standing/normal-sort/POST/normalSort`),
                  method: 'post',
                  data: this.$http.adornData({
                    'bzflbm': this.dataForm.bzflbm,
                    'bzflmc': this.dataForm.bzflmc,
                    'sjflid': this.dataForm.sjflid,
                    'sfgwbz': this.dataForm.sfgwbz,
                    'gwbzdm': this.dataForm.gwbzdm,
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
