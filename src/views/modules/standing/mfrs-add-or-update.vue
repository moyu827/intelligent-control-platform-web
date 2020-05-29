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
          <el-form-item label="生产厂家编码" prop="sccjbm">
            <el-input v-model="dataForm.sccjbm" ></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="生产厂家名称" prop="sccjmc">
            <el-input v-model="dataForm.sccjmc" ></el-input>
          </el-form-item>
        </el-col>
      </el-row>

      <el-row>
        <el-col :span="12">
          <el-form-item label="联系方式" prop="lxfs">
            <el-input v-model="dataForm.lxfs" ></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="国家" prop="gj">
            <el-input v-model="dataForm.gj" ></el-input>
          </el-form-item>
        </el-col>
      </el-row>

      <el-row>
        <el-col :span="12">
          <el-form-item label="城市" prop="cs">
            <el-input v-model="dataForm.cs" ></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="地址" prop="dz">
            <el-input v-model="dataForm.dz" ></el-input>
          </el-form-item>
        </el-col>
      </el-row>


      <el-row>
        <el-col :span="12">
          <el-form-item label="备注" prop="bz">
            <el-input  type="textarea"  resize="none" v-model="dataForm.bz" ></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="MDM编码" prop="mdmbm">
            <el-input v-model="dataForm.mdmbm" ></el-input>
          </el-form-item>
        </el-col>
      </el-row>

<!--      <el-row>-->
<!--        <el-col :span="12">-->
<!--          <el-form-item label="设备大类名称" prop="sbdlbm">-->
<!--            <el-input v-model="dataForm.sbdlbm" ></el-input>-->
<!--          </el-form-item>-->
<!--        </el-col>-->
<!--        <el-col :span="12">-->
<!--          <el-form-item label="设备类型名称" prop="sblxbm">-->
<!--            <el-input v-model="dataForm.sblxbm" ></el-input>-->
<!--          </el-form-item>-->
<!--        </el-col>-->
<!--      </el-row>-->

      <el-row>
        <el-col :span="12">
          <el-form-item label="地区" prop="dq">
            <el-input v-model="dataForm.dq" ></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="经营范围" prop="jyfw">
            <el-input type="textarea"  resize="none" v-model="dataForm.jyfw" ></el-input>
          </el-form-item>
        </el-col>
      </el-row>

<!--      <el-row>-->
<!--        <el-col :span="12">-->
<!--          <el-form-item label="厂商识别信息" prop="cssbxx">-->
<!--            <el-input v-model="dataForm.cssbxx" ></el-input>-->
<!--          </el-form-item>-->
<!--        </el-col>-->
<!--        <el-col :span="12">-->
<!--          <el-form-item label="家族号唯一标识" prop="fomdid">-->
<!--            <el-input v-model="dataForm.fomdid" ></el-input>-->
<!--          </el-form-item>-->
<!--        </el-col>-->
<!--      </el-row>-->
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>
<style>
  textarea{
    resize:none
  }
</style>
<script>
    export default {
      data () {
        return {
          visible: false,
          dataForm: {
            objId: 0,
            sccjbm: '',
            sccjmc: '',
            // cym: '',
            gj: '',
            cs: '',
            dz: '',
            lxfs: '',
            bz: '',
            mdmbm: '',
            dq: '',
            jyfw: ''
          },
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
        init (id) {
          this.visible = true
          this.dataForm.objId = id || 0
          if (id !== undefined && id !== 0) {
            this.$http({
              url: this.$http.adornUrl(`/standing/mfrs/GET/mfrs`),
              method: 'get',
              params: this.$http.adornParams({
                'objId': id
              })
            }).then(({data}) => {
              if (data.status === 'success') {
                this.dataForm.objId = data.rows[0].objId
                this.dataForm.sccjbm = data.rows[0].sccjbm
                this.dataForm.sccjmc = data.rows[0].sccjmc
                this.dataForm.gj = data.rows[0].gj
                this.dataForm.cs = data.rows[0].cs
                this.dataForm.dz = data.rows[0].dz
                this.dataForm.lxfs = data.rows[0].lxfs
                this.dataForm.bz = data.rows[0].bz
                this.dataForm.mdmbm = data.rows[0].mdmbm
                this.dataForm.dq = data.rows[0].dq
                this.dataForm.jyfw = data.rows[0].jyfw
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
                  url: this.$http.adornUrl(`/standing/mfrs/PUT/mfrs`),
                  method: 'put',
                  data: this.$http.adornData({
                    'objId': objId,
                    'sccjbm': this.dataForm.sccjbm,
                    'sccjmc': this.dataForm.sccjmc,
                    'gj': this.dataForm.gj,
                    'cs': this.dataForm.cs,
                    'dz': this.dataForm.dz,
                    'lxfs': this.dataForm.lxfs,
                    'bz': this.dataForm.bz,
                    'mdmbm': this.dataForm.mdmbm,
                    'dq': this.dataForm.dq,
                    'jyfw': this.dataForm.jyfw
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
                  url: this.$http.adornUrl(`/standing/mfrs/POST/mfrs`),
                  method: 'post',
                  data: this.$http.adornData({
                    'sccjbm': this.dataForm.sccjbm,
                    'sccjmc': this.dataForm.sccjmc,
                    'gj': this.dataForm.gj,
                    'cs': this.dataForm.cs,
                    'dz': this.dataForm.dz,
                    'lxfs': this.dataForm.lxfs,
                    'bz': this.dataForm.bz,
                    'mdmbm': this.dataForm.mdmbm,
                    'dq': this.dataForm.dq,
                    'jyfw': this.dataForm.jyfw
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
