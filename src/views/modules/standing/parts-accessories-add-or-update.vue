<template>
  <el-dialog
    :title="!dataForm.objId ? '新增' : '修改'"
    :close-on-click-modal="false"
    width="720px"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="120px">
      <el-form-item style="display: none" prop="objId">
        <el-input v-model="dataForm.objId"></el-input>
      </el-form-item>
      <el-row>
        <el-col :span="12">
          <el-form-item label="所属设备" prop="sssb">
            <el-select  v-model="dataForm.sssb"  style="width: 220px;" filterable @change="changeSssb"  placeholder="请选择">
              <el-option
                v-for="item in sssbList"
                :key="item.id"
                :label="item.name"
                :value="item.id">
              </el-option>
            </el-select>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="设备编码" prop="sbbm">
            <el-input v-model="dataForm.sbbm"></el-input>
          </el-form-item>
        </el-col>
      </el-row>

      <el-row>
        <el-col :span="12">
          <el-form-item label="设备名称" prop="sbmc">
            <el-input v-model="dataForm.sbmc"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="设备类型" prop="sblx">
            <el-select  v-model="dataForm.sblx" :filter-method="sblxFilter"  style="width: 220px;" filterable @change="changeSblx"  placeholder="请选择">
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
        <el-col :span="12">
          <el-form-item label="型号" prop="xh">
            <el-input v-model="dataForm.xh"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="生产厂家" prop="sccj">
            <el-select  v-model="dataForm.sccj"  :filter-method="sccjFilter" style="width: 220px;" filterable    clearable placeholder="请选择">
              <el-option
                v-for="item in sccjList"
                :key="item.sccjbm"
                :label="item.sccjmc"
                :value="item.sccjbm">
              </el-option>
            </el-select>
          </el-form-item>
        </el-col>
      </el-row>


      <el-row>
        <el-col :span="12">
          <el-form-item label="制造国家" prop="zzgj">
            <el-input v-model="dataForm.zzgj"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="出厂编号" prop="ccbh">
            <el-input v-model="dataForm.ccbh"></el-input>
          </el-form-item>
        </el-col>
      </el-row>

      <el-row>
        <el-col :span="12">
          <el-form-item label="出厂日期" prop="ccrq">
            <el-date-picker
              v-model="dataForm.ccrq"
              type="date"
              value-format="yyyy-MM-dd"
              placeholder="选择日期">
            </el-date-picker>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="投运日期" prop="tyrq">
            <el-date-picker
              v-model="dataForm.tyrq"
              type="date"
              value-format="yyyy-MM-dd"
              placeholder="选择日期">
            </el-date-picker>
          </el-form-item>
        </el-col>
      </el-row>

      <el-row>

          <el-col :span="12">
            <el-form-item label="所属地市" prop="ssds">
              <el-select  v-model="dataForm.ssds"  style="width: 220px;" filterable @change="changeSsds"     placeholder="请选择">
                <el-option
                  v-for="item in ssdsList"
                  :key="item.id"
                  :label="item.name"
                  :value="item.id">
                </el-option>
              </el-select>
            </el-form-item>
          </el-col>


        <el-col :span="12">
          <el-form-item label="站房" prop="zf">
            <el-select  v-model="dataForm.zf"  style="width: 220px;" filterable @change="changeZf"   clearable   placeholder="请选择">
              <el-option
                v-for="item in zfList"
                :key="item.id"
                :label="item.name"
                :value="item.id">
              </el-option>
            </el-select>
          </el-form-item>
        </el-col>
      </el-row>

      <el-row>
        <el-col :span="12">
        <el-form-item label="产品代号" prop="cpdh">
          <el-input v-model="dataForm.cpdh"></el-input>
        </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="登记时间" prop="djsj">
            <el-date-picker
              v-model="dataForm.djsj"
              type="datetime"
              value-format="yyyy-MM-dd HH:mm:ss"
              placeholder="选择日期">
            </el-date-picker>
          </el-form-item>
        </el-col>
<!--        <el-col :span="12">-->
<!--          <el-form-item label="运维单位" prop="ywdw">-->
<!--            <el-select  v-model="dataForm.ywdw"  style="width: 220px;" filterable @change="changeYwdw"     placeholder="请选择">-->
<!--              <el-option-->
<!--                v-for="item in ywdwList"-->
<!--                :key="item.id"-->
<!--                :label="item.name"-->
<!--                :value="item.id">-->
<!--              </el-option>-->
<!--            </el-select>-->
<!--          </el-form-item>-->
<!--        </el-col>-->
      </el-row>

      <el-row>
<!--        <el-col :span="12">-->
<!--          <el-form-item label="维护班组" prop="whbz">-->
<!--            <el-select  v-model="dataForm.whbz"  style="width: 220px;" filterable @change="changeWhbz"     placeholder="请选择">-->
<!--              <el-option-->
<!--                v-for="item in whbzList"-->
<!--                :key="item.id"-->
<!--                :label="item.name"-->
<!--                :value="item.id">-->
<!--              </el-option>-->
<!--            </el-select>-->
<!--          </el-form-item>-->
<!--        </el-col>-->

      </el-row>

      <el-row>
<!--        <el-col :span="12">-->
<!--          <el-form-item label="设备主人" prop="sbzr">-->
<!--            <el-select  v-model="dataForm.sbzr"  style="width: 220px;" filterable @change="changeSbzr"     placeholder="请选择">-->
<!--              <el-option-->
<!--                v-for="item in sbzrList"-->
<!--                :key="item.id"-->
<!--                :label="item.name"-->
<!--                :value="item.id">-->
<!--              </el-option>-->
<!--            </el-select>-->
<!--          </el-form-item>-->
<!--        </el-col>-->
        <el-col :span="12">
          <el-form-item label="运行状态" prop="yxzt">
            <el-select  v-model="dataForm.yxzt"  style="width: 220px;" filterable      placeholder="请选择">
              <el-option
                v-for="item in yxztList"
                :key="item.id"
                :label="item.name"
                :value="item.id">
              </el-option>
            </el-select>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="专业分类" prop="zyfl">
            <el-select  v-model="dataForm.zyfl"  style="width: 220px;" filterable      placeholder="请选择">
              <el-option
                v-for="item in zyflList"
                :key="item.id"
                :label="item.name"
                :value="item.id">
              </el-option>
            </el-select>
          </el-form-item>
        </el-col>
      </el-row>


      <el-row>
<!--        <el-col :span="12">-->
<!--          <el-form-item label="发布状态" prop="fbzt">-->
<!--            <el-input v-model="dataForm.fbzt"></el-input>-->
<!--          </el-form-item>-->
<!--        </el-col>-->
<!--        <el-col :span="12">-->
<!--          <el-form-item label="专业分类" prop="zyfl">-->
<!--            <el-select  v-model="dataForm.zyfl"  style="width: 220px;" filterable      placeholder="请选择">-->
<!--              <el-option-->
<!--                v-for="item in zyflList"-->
<!--                :key="item.id"-->
<!--                :label="item.name"-->
<!--                :value="item.id">-->
<!--              </el-option>-->
<!--            </el-select>-->
<!--          </el-form-item>-->
<!--        </el-col>-->
      </el-row>

      <el-row>
        <el-col :span="24">
          <el-form-item label="备注" prop="bz">
            <el-input  type="textarea"  resize="none" v-model="dataForm.bz"></el-input>
          </el-form-item>
        </el-col>
      </el-row>

      <el-row v-if="dataForm.objId">
        <el-col :span="24">
          <el-form-item label="数据来源" prop="sjly">
            <el-select  v-model="dataForm.sjly"  style="width: 220px;" filterable      placeholder="请选择">
              <el-option
                v-for="item in sjlyList"
                :key="item.id"
                :label="item.name"
                :value="item.id">
              </el-option>
            </el-select>
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
<style>
  .el-select-dropdown__item {
    font-size: 14px;
    padding: 0 20px;
    position: relative;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    color: #606266;
    height: 34px;
    line-height: 34px;
    -webkit-box-sizing: border-box;
    box-sizing: border-box;
    cursor: pointer;
    width: 200px;
  }
</style>
<script>
    export default {
      data () {
        return {
          visible: false,
          dataForm: {
            objId: 0,
            sssb: '',
            sbbm: '',
            sbmc: '',
            sblx: '',
            xh: '',
            sccj: '',
            zzgj: '',
            ccbh: '',
            ccrq: '',
            tyrq: '',
            cpdh: '',
            zf: '',
            ssds: '',
            ywdw: '',
            whbz: '',
            djsj: new Date(),
            sbzr: '',
            ssdsmc: '',
            ywdwmc: '',
            whbzmc: '',
            sbzrmc: '',
            sblxmc: '',
            yxzt: '',
            sssbmc: '',
            zfmc: '',
            fbzt: '',
            zyfl: '',
            sjly: '0',
            bz: ''
          },
          dataRule: {
            // name: [
            //             { required: true, message: '电压等级名称不能为空', trigger: 'blur' }
            // ]
          },
          sssbList: [{
            id: '11101001281080',
            name: '1号主变'
          }, {
            id: '11101001284750',
            name: '2号主变'
          }, {
            id: '11101003152510',
            name: '3号主变'
          }, {
            id: '11102000016590',
            name: '2号主变压器'
          }],
          sblxList: [],
          allSblxList: [],
          sccjList: [],
          allSccjList: [],
          zfList: [],
          ssdsList: [],

          ywdwList: [{
            id: '8a1ea5c64bdebad1014bdebc5c21053d',
            name: '国网枣庄供电公司'
          }, {
            id: '8a1ea5c64bdebad1014bdebc6047087b',
            name: '国网山东省电力公司检修公司'
          }, {
            id: '8a1ea5c64bdebad1014bdebc55c80318',
            name: '国网威海供电公司'
          }, {
            id: '8a1ea5c64bdebad1014bdebc55c80366',
            name: '文登市供电公司'
          }],

          whbzList: [{
            id: '8a1ea5c64bdebad1014bdebc55c8004d',
            name: '运维二班'
          }, {
            id: '8a1ea5c64bdebad1014bdebc5c210573',
            name: '变电运维二班'
          }, {
            id: '8a1ea5c64bdebad1014bdebc604708b4',
            name: '山东电力集团公司检修公司'
          }, {
            id: '8a1ea5c64bdebad1014bdebc55c80079',
            name: '青岛供电公司'
          }],
          yxztList: [{
            id: '10',
            name: '未投运'
          }, {
            id: '20',
            name: '在运'
          }, {
            id: '30',
            name: '退役'
          },
          {
            id: '31',
            name: '现场留用'
          },
          {
            id: '32',
            name: '库存备用'
          },
          {
            id: '33',
            name: '待报废'
          }, {
            id: '40',
            name: '报废'
          }],
          zyflList: [{
            id: '1',
            name: '输电'
          }, {
            id: '2',
            name: '变电'
          }, {
            id: '3',
            name: '配电'
          }],
          sjlyList: [{
            id: '0',
            name: '本地主站和APP端'
          }, {
            id: '1',
            name: 'ERP'
          }],

          sbzrList: [{
            id: 'E885DB89EE2A675CE0408D0A5B0463A0',
            name: '王栋'
          }, {
            id: '陈兵',
            name: '陈兵'
          }, {
            id: '周长春',
            name: '周长春'
          }, {
            id: '翟文涛',
            name: '翟文涛'
          }]

        }
      },
      components: {
      },
      methods: {
        changeSssb (val) {
          let sssbList = this.sssbList
          if (sssbList.length > 0) {
            for (const i in this.sssbList) {
              if (sssbList[i].id === val) {
                this.dataForm.ssdsmc = sssbList[i].name
              }
            }
          }
        },
        changeSblx (val) {
          let sblxList = this.sblxList
          if (sblxList.length > 0) {
            for (const i in this.sblxList) {
              if (sblxList[i].sblxbm === val) {
                this.dataForm.sblxmc = sblxList[i].sblx
              }
            }
          }
        },
        changeZf (val) {
          let zfList = this.zfList
          if (zfList.length > 0) {
            for (const i in this.zfList) {
              if (zfList[i].id === val) {
                this.dataForm.zfmc = zfList[i].name
              }
            }
          }
        },
        changeSsds (val, number) {
          let ssdsList = this.ssdsList
          if (undefined === number) {
            this.dataForm.zf = ''
            this.dataForm.zfmc = ''
          }
          if (ssdsList.length > 0) {
            for (const i in this.ssdsList) {
              if (ssdsList[i].id === val) {
                this.dataForm.ssdsmc = ssdsList[i].name
                this.zfList = ssdsList[i].list
              }
            }
          }
        },
        changeYwdw (val) {
          let ywdwList = this.ywdwList
          if (ywdwList.length > 0) {
            for (const i in this.ywdwList) {
              if (ywdwList[i].id === val) {
                this.dataForm.ywdwmc = ywdwList[i].name
              }
            }
          }
        },
        changeWhbz (val) {
          let whbzList = this.whbzList
          if (whbzList.length > 0) {
            for (const i in this.whbzList) {
              if (whbzList[i].id === val) {
                this.dataForm.whbzmc = whbzList[i].name
              }
            }
          }
        },
        changeSbzr (val) {
          let sbzrList = this.sbzrList
          if (sbzrList.length > 0) {
            for (const i in this.sbzrList) {
              if (sbzrList[i].id === val) {
                this.dataForm.sbzrmc = sbzrList[i].name
              }
            }
          }
        },
        sccjFilter (query = '') {
          let arr = this.allSccjList.filter((item) => {
            // return (item.sccjbm !== undefined && item.sccjbm !== '' && item.sccjbm.includes(query)) || (item.sccjmc !== undefined && item.sccjmc !== '' && item.sccjmc.includes(query))
            return item.sccjbm.includes(query) || item.sccjmc.includes(query)
          })
          if (arr.length > 50) {
            this.sccjList = arr.slice(0, 50)
          } else {
            this.sccjList = arr
          }
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
        getsccj () {
          this.$http({
            url: this.$http.adornUrl(`/select/GET/selectMfrs`),
            method: 'get',
            params: this.$http.adornParams({
            })
          }).then(({data}) => {
            if (data.status === 'success') {
              if (data.rows.length > 0) {
                this.allSccjList = data.rows
                this.sccjFilter()
              }
            } else {
              this.allSccjList = []
            }
          })
        },
        getdszf () {
          this.$http({
            url: this.$http.adornUrl(`/bus/sub-control-area/GET/subControlAreas/select`),
            method: 'get',
            params: this.$http.adornParams({
            })
          }).then(({data}) => {
            if (data.status === 'success') {
              if (data.rows.length > 0) {
                this.ssdsList = data.rows
              }
            } else {
              this.ssdsList = []
            }
          })
        },
        init (id) {
          this.zfList = []
          this.visible = true
          this.getsblx()
          this.getsccj()
          this.getdszf()
          this.dataForm.id = id || 0
          if (id !== undefined && id !== 0) {
            this.$http({
              url: this.$http.adornUrl(`/standing/parts-accessories/GET/partsAccessories`),
              method: 'get',
              params: this.$http.adornParams({
                'objId': id
              })
            }).then(({data}) => {
              if (data.status === 'success') {
                this.sblxFilter(data.rows[0].sblx)
                this.sccjFilter(data.rows[0].sccj)
                this.dataForm.objId = data.rows[0].objId
                this.dataForm.sssb = data.rows[0].sssb
                this.dataForm.sbbm = data.rows[0].sbbm
                this.dataForm.sbmc = data.rows[0].sbmc
                this.dataForm.sblx = data.rows[0].sblx
                this.dataForm.xh = data.rows[0].xh
                this.dataForm.sccj = data.rows[0].sccj
                this.dataForm.zzgj = data.rows[0].zzgj
                this.dataForm.ccbh = data.rows[0].ccbh
                this.dataForm.ccrq = data.rows[0].ccrq
                this.dataForm.tyrq = data.rows[0].tyrq
                this.dataForm.cpdh = data.rows[0].cpdh
                if (data.rows[0].zf !== '' && data.rows[0].zf !== undefined) {
                  this.dataForm.zf = parseInt(data.rows[0].zf)
                } else {
                  this.dataForm.zf = data.rows[0].zf
                }
                if (data.rows[0].ssds !== '' && data.rows[0].ssds !== undefined) {
                  this.dataForm.ssds = parseInt(data.rows[0].ssds)
                  this.changeSsds(parseInt(data.rows[0].ssds), 1)
                } else {
                  this.dataForm.ssds = data.rows[0].ssds
                }
                this.dataForm.ywdw = data.rows[0].ywdw
                this.dataForm.whbz = data.rows[0].whbz
                this.dataForm.djsj = data.rows[0].djsj
                this.dataForm.sbzr = data.rows[0].sbzr
                this.dataForm.yxzt = data.rows[0].yxzt
                this.dataForm.bz = data.rows[0].bz
                this.dataForm.fbzt = data.rows[0].fbzt
                this.dataForm.zyfl = data.rows[0].zyfl
                this.dataForm.sjly = data.rows[0].sjly
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
              let id = this.dataForm.objId
              if (id !== undefined && id !== 0) {
                this.$http({
                  url: this.$http.adornUrl(`/standing/parts-accessories/PUT/partsAccessories`),
                  method: 'put',
                  data: this.$http.adornData({
                    'objId': id,
                    'sssb': this.dataForm.sssb,
                    'sbbm': this.dataForm.sbbm,
                    'sbmc': this.dataForm.sbmc,
                    'sblx': this.dataForm.sblx,
                    'xh': this.dataForm.xh,
                    'sccj': this.dataForm.sccj,
                    'zzgj': this.dataForm.zzgj,
                    'ccbh': this.dataForm.ccbh,
                    'ccrq': this.dataForm.ccrq,
                    'tyrq': this.dataForm.tyrq,
                    'cpdh': this.dataForm.cpdh,
                    'zf': this.dataForm.zf,
                    'ssds': this.dataForm.ssds,
                    'ywdw': this.dataForm.ywdw,
                    'whbz': this.dataForm.whbz,
                    'djsj': this.dataForm.djsj,
                    'sbzr': this.dataForm.sbzr,
                    'ssdsmc': this.dataForm.ssdsmc,
                    'ywdwmc': this.dataForm.ywdwmc,
                    'whbzmc': this.dataForm.whbzmc,
                    'sbzrmc': this.dataForm.sbzrmc,
                    'sblxmc': this.dataForm.sblxmc,
                    'yxzt': this.dataForm.yxzt,
                    'sssbmc': this.dataForm.sssbmc,
                    'zfmc': this.dataForm.zfmc,
                    'fbzt': this.dataForm.fbzt,
                    'zyfl': this.dataForm.zyfl,
                    'sjly': this.dataForm.sjly,
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
                  url: this.$http.adornUrl(`/standing/parts-accessories/POST/partsAccessories`),
                  method: 'post',
                  data: this.$http.adornData({
                    'sssb': this.dataForm.sssb,
                    'sbbm': this.dataForm.sbbm,
                    'sbmc': this.dataForm.sbmc,
                    'sblx': this.dataForm.sblx,
                    'xh': this.dataForm.xh,
                    'sccj': this.dataForm.sccj,
                    'zzgj': this.dataForm.zzgj,
                    'ccbh': this.dataForm.ccbh,
                    'ccrq': this.dataForm.ccrq,
                    'tyrq': this.dataForm.tyrq,
                    'cpdh': this.dataForm.cpdh,
                    'zf': this.dataForm.zf,
                    'ssds': this.dataForm.ssds,
                    'ywdw': this.dataForm.ywdw,
                    'whbz': this.dataForm.whbz,
                    'djsj': this.dataForm.djsj,
                    'sbzr': this.dataForm.sbzr,
                    'ssdsmc': this.dataForm.ssdsmc,
                    'ywdwmc': this.dataForm.ywdwmc,
                    'whbzmc': this.dataForm.whbzmc,
                    'sbzrmc': this.dataForm.sbzrmc,
                    'sblxmc': this.dataForm.sblxmc,
                    'yxzt': this.dataForm.yxzt,
                    'sssbmc': this.dataForm.sssbmc,
                    'zfmc': this.dataForm.zfmc,
                    'fbzt': this.dataForm.fbzt,
                    'zyfl': this.dataForm.zyfl,
                    'sjly': this.dataForm.sjly,
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
