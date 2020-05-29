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
          <el-form-item label="设备名称" prop="name">
            <el-input v-model="dataForm.name" placeholder="设备名称"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="设备编号" prop="code">
            <el-input v-model="dataForm.code" placeholder="设备编号"></el-input>
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
      <el-row>
        <el-col :span="12">
          <el-form-item label="所属组织" prop="orgCode">
            <!--<el-input v-model="dataForm.orgCode" placeholder="所属组织"></el-input>-->
            <SelectTree
              :props="props"
              :name="treeName"
              :options="optionData"
              :value="valueId"
              :clearable="isClearable"
              :accordion="isAccordion"
              @getValue="getValue($event)"/>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item  label="责任人" prop="contactor">
            <!--<el-input v-model="dataForm.contactor" placeholder="责任人"></el-input>-->
            <el-select
              v-model="dataForm.contactor"
              multiple
              size="400px"
              clearable
              filterable
              remote
              placeholder="输入用户名搜索"
              :remote-method="remoteMethod">
              <el-option
                v-for="item in options"
                :key="item.id"
                :label="item.userName"
                :value="item.id">
              </el-option>
            </el-select>
          </el-form-item>
        </el-col>
      </el-row>
      <el-form-item label="安装位置" prop="location">
        <el-input v-model="dataForm.location" placeholder="安装位置"></el-input>
      </el-form-item>

      <el-form-item label="图片" >
        <el-upload
          v-model="dataForm.pictureList"
          :headers="myheader"
          action="/proxyApi/backstage/POST/equipment-picture-upload"
          name="file"
          :limit="3"
          :on-exceed="pictureLimit"
          :before-remove="removePicture"
          :before-upload="pictureBeforeUpload"
          :on-success="pictureUploadSuccess"
          :file-list="pictureList">
          <el-button  size="small" type="primary">点击上传</el-button>
          <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过1M</div>
        </el-upload>
      </el-form-item>
      <el-form-item label="附件" >
        <el-upload
          v-model="dataForm.attachmentList"
          :headers="myheader"
          action="/proxyApi/backstage/POST/equipment-file-upload"
          name="file"
          :limit="1"
          :before-remove="removeFile"
          :before-upload="fileBeforeUpload"
          :on-success="fileUploadSuccess"
          :on-exceed="fileLimit"
          :file-list="fileList">
          <el-button size="small" type="primary">点击上传</el-button>
          <div slot="tip" class="el-upload__tip">文件不能超过10M</div>
        </el-upload>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  import SelectTree from '../../../components/treeSelect'
  import Vue from 'vue'
  export default {
    data () {
      return {
        options: [],
        valueId: 0,
        treeName: '',
        optionData: this.getTree(),
        props: {
          value: 'id',
          children: 'children',
          label: 'label'
        },
        isClearable: false,     // 可清空（可选）
        isAccordion: true,     // 可收起（可选）
        myheader: {Authorization: Vue.cookie.get('Authorization')},
        uploadFileList: [],
        uploadPictureList: [],
        fileList: [],
        pictureList: [],
        visible: false,
        dataForm: {
          id: 0,
          name: '',
          code: '',
          model: '',
          manufacturer: '',
          orgCode: '',
          contactor: [],
          location: '',
          pictureList: '',
          attachmentList: ''
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
          orgCode: [
            { required: true, message: '所属组织不能为空', trigger: 'blur' }
          ],
          contactor: [
            { required: true, message: '责任人不能为空', trigger: 'blur' }
          ],
          location: [
            { required: true, message: '安装位置不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    components: {
      SelectTree
    },
    methods: {
      userClick: function () {
        alert(123)
      },
      pictureLimit (files, fileList) {
        this.$alert('最多能上传3个图片')
      },
      fileLimit (files, fileList) {
        this.$alert('最多能上传1个附件')
      },
      reseverchange () {
        this.$message({
          duration: 1,
          onClose: () => {
            this.visible = false
            this.$emit('refReload')
          }
        })
      },
      remoteMethod (query) {
        this.options = []
        this.$http({
          url: this.$http.adornUrl('/backstage/GET/sysUsers'),
          method: 'post',
          params: this.$http.adornParams({
            'username': query
          })
        }).then(({data}) => {
          if (data.status === 'success') {
            console.log(data.rows)
            this.options = data.rows
          } else {
            this.options = []
          }
        })
      },
      getValue (value) {
        this.valueId = value
        this.dataForm.orgCode = value
      },
      getTree () {
        this.dataListLoading = true
        let s = []
        this.$http({
          url: this.$http.adornUrl('/backstage/GET/web/sysOrgTree'),
          method: 'get'
        }).then(({data}) => {
          for (let i in data.rows) {
            s.push(data.rows[i])
          }
          this.dataListLoading = false
        })
        return s
      },
      removePicture (file, fileList) {
        let name = file.name
        let fileSize = file.size
        let fileType = name.split('.')[1]
        let jpg = fileType === 'jpg'
        let png = fileType === 'png'
        let gif = fileType === 'gif'
        let jpeg = fileType === 'jpeg'
        let pcx = fileType === 'pcx'
        let psd = fileType === 'psd'
        let tiff = fileType === 'tiff'
        let svg = fileType === 'svg'
        if (!jpg || !png || !gif || !jpeg || !pcx || !psd || !tiff || !svg) {
          return true
        } else if (fileSize > 1048576) {
          return true
        } else {
          return this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(() => {
            this.deletePicture(file)
          })
        }
      },
      removeFile (file, fileList) {
        let fileSize = file.size
        if (fileSize > 10485760) {
          return true
        } else {
          return this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(() => {
            this.deleteFile(file)
          })
        }
      },
      deletePicture (file) {
        let name = file.name
        this.uploadPictureList.splice(this.uploadPictureList.findIndex(item => item.name === name), 1)
      },
      deleteFile (file) {
        let name = file.name
        this.uploadFileList.splice(this.uploadFileList.findIndex(item => item.name === name), 1)
      },
      fileUploadSuccess (response, file, fileList) {
        let newFile = {}
        newFile.name = response.rows.name
        newFile.url = response.rows.url
        this.uploadFileList.push(newFile)
      },
      pictureUploadSuccess (response, file, fileList) {
        let newFile = {}
        newFile.name = response.rows.name
        newFile.url = response.rows.url
        this.uploadPictureList.push(newFile)
      },
      pictureBeforeUpload (file) {
        let name = file.name
        let fileSize = file.size
        let fileType = name.split('.')[1]
        let jpg = fileType === 'jpg'
        let png = fileType === 'png'
        let gif = fileType === 'gif'
        let jpeg = fileType === 'jpeg'
        let pcx = fileType === 'pcx'
        let psd = fileType === 'psd'
        let tiff = fileType === 'tiff'
        let svg = fileType === 'svg'
        if (!jpg || !png || !gif || !jpeg || !pcx || !psd || !tiff || !svg) {
          this.$alert('请上传jpg,png,gif,jpeg,pcx,psd,tiff,svg这些格式的图片')
          return false
        } else if (fileSize > 1048576) {
          this.$alert('文件最大不能超过1M')
          return false
        } else {
          return true
        }
      },
      fileBeforeUpload (file) {
        let fileSize = file.size
        if (fileSize > 10485760) {
          this.$alert('文件最大不能超过10M')
          return false
        } else {
          return true
        }
      },
      init (id) {
        this.visible = true
        this.dataForm.id = id || 0
        if (id !== undefined && id !== 0) {
          this.valueId = 10001
          this.treeName = '云海贝贝'
          this.options = [{id: 295296798769152,userName: 'haha'}]
          this.dataForm.contactor = [{id: 295296798769152}]
        } else {

        }
        // this.$http({
        //   url: this.$http.adornUrl('/sys/role/select'),
        //   method: 'get',
        //   params: this.$http.adornParams()
        // }).then(({data}) => {
        //   this.roleList = data && data.code === 0 ? data.list : []
        // }).then(() => {
        //   this.visible = true
        //   this.$nextTick(() => {
        //     this.$refs['dataForm'].resetFields()
        //   })
        // }).then(() => {
        //   if (this.dataForm.id) {
        //     this.$http({
        //       url: this.$http.adornUrl(`/sys/user/info/${this.dataForm.id}`),
        //       method: 'get',
        //       params: this.$http.adornParams()
        //     }).then(({data}) => {
        //       if (data && data.code === 0) {
        //         this.dataForm.userName = data.user.username
        //         this.dataForm.salt = data.user.salt
        //         this.dataForm.email = data.user.email
        //         this.dataForm.mobile = data.user.mobile
        //         this.dataForm.roleIdList = data.user.roleIdList
        //         this.dataForm.status = data.user.status
        //       }
        //     })
        //   }
        // })
      },
      // 表单提交
      dataFormSubmit () {
        let fileStr = ''
        if (this.uploadFileList.length > 0) {
          let fileList = []
          for (let i in this.uploadFileList) {
            fileList.push(this.uploadFileList[i].url)
          }
          fileStr = fileList.join(',')
        }
        let pictureStr = ''
        if (this.uploadPictureList.length > 0) {
          let pictureList = []
          for (let i in this.uploadPictureList) {
            pictureList.push(this.uploadPictureList[i].url)
          }
          pictureStr = pictureList.join(',')
        }

        console.log(this.dataForm.contactor.join(','))
        // this.dataForm.attachmentList = this.uploadFileList
        // this.dataForm.pictureList = this.uploadPictureList
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/backstage/POST/web/device-equipment`),
              method: 'post',
              data: this.$http.adornData({
                'name': this.dataForm.name,
                'code': this.dataForm.code,
                'model': this.dataForm.model,
                'manufacturer': this.dataForm.manufacturer,
                'orgCode': this.dataForm.orgCode,
                'contactor': this.dataForm.contactor.join(','),
                'location': this.dataForm.location,
                'picture': pictureStr,
                'attachment': fileStr
              })
            }).then(({data}) => {
              if (data.status === 'success') {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    // this.$emit('refReload')
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.message)
              }
            })
          }
        })
      }
    }
  }
</script>
