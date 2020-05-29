<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :before-close="reseverchange"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item style="display: none">
        <el-input v-model="dataForm.id" placeholder="规格型号"></el-input>
      </el-form-item>
      <el-row>
        <el-col :span="12">
          <el-form-item label="备件名称" prop="name">
            <el-input v-model="dataForm.name" placeholder="备件名称"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="备件编号" prop="code">
            <el-input v-model="dataForm.code" placeholder="备件编号"></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="12">
          <el-form-item label="规格型号" prop="model">
            <el-input v-model="dataForm.model" placeholder="规格型号"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="生产厂家" prop="manufacturer">
            <el-input v-model="dataForm.manufacturer" placeholder="生产厂家"></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-form-item label="数量" prop="storageNum">
        <el-input-number :min="0" :step="1" v-model="dataForm.storageNum" ></el-input-number>
      </el-form-item>
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
          code: '',
          model: '',
          manufacturer: '',
          storageNum: 0
        },
        dataRule: {
          name: [
            { required: true, message: '设备名称不能为空', trigger: 'blur' }
          ],
          code: [
            { required: true, message: '设备编号不能为空', trigger: 'blur' }
          ],
          model: [
            { required: true, message: '规格型号不能为空', trigger: 'blur' }
          ],
          manufacturer: [
            { required: true, message: '生产厂家不能为空', trigger: 'blur' }
          ],
          storageNum: [
            { required: true, message: '数量不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      reseverchange () {
        this.$message({
          duration: 1,
          onClose: () => {
            this.visible = false
            this.$emit('refReload')
          }
        })
      },
      init (id) {
        this.visible = true
        this.dataForm.id = id || 0
        console.log(id)
        if (id !== undefined && id !== 0) {
          this.$http({
            url: this.$http.adornUrl(`/backstage/GET/reserveComponents`),
            method: 'post',
            data: this.$http.adornData({
              'id': id
            })
          }).then(({data}) => {
            if (data.status === 'success') {
              this.dataForm.id = data.rows[0].id
              this.dataForm.name = data.rows[0].name
              this.dataForm.code = data.rows[0].code
              this.dataForm.model = data.rows[0].model
              this.dataForm.manufacturer = data.rows[0].manufacturer
              this.dataForm.storageNum = data.rows[0].storageNum
            } else {
              this.$message.error(data.message)
            }
          })
        }
      },
      // 表单提交
      dataFormSubmit () {
        let num = this.dataForm.storageNum
        if (!(/(^[1-9]\d*$)/.test(num))) {
          this.$alert('请输入正整数')
          return
        }
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            let id = this.dataForm.id
            if (id !== undefined && id !== 0) {
              this.$http({
                url: this.$http.adornUrl(`/backstage/PUT/reserveComponent`),
                method: 'post',
                data: this.$http.adornData({
                  'id': id,
                  'name': this.dataForm.name,
                  'code': this.dataForm.code,
                  'model': this.dataForm.model,
                  'manufacturer': this.dataForm.manufacturer,
                  'storageNum': this.dataForm.storageNum
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
                url: this.$http.adornUrl(`/backstage/POST/reserveComponent`),
                method: 'post',
                data: this.$http.adornData({
                  'name': this.dataForm.name,
                  'code': this.dataForm.code,
                  'model': this.dataForm.model,
                  'manufacturer': this.dataForm.manufacturer,
                  'storageNum': this.dataForm.storageNum
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
