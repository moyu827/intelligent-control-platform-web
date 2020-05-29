<template>
  <el-dialog
    :title="!dataForm.objId ? '新增' : '修改'"
    :close-on-click-modal="false"
    width="50%"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="120px">
      <el-form-item style="display: none">
        <el-input v-model="dataForm.objId"></el-input>
      </el-form-item>
      <el-row>
        <el-col :span="8">
          <el-form-item label="设备类型编码" prop="sblxbm">
            <el-input v-model="dataForm.sblxbm" placeholder=""></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="设备类型" prop="sblx">
            <el-input v-model="dataForm.sblx" placeholder=""></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="父设备类型" prop="fsblxid">
            <el-select  v-model="dataForm.fsblxid"  filterable @change="changeZsb"   placeholder="请选择">
              <el-option
                v-for="item in fsblxList"
                :key="item.id"
                :label="item.name"
                :value="item.id">
              </el-option>
            </el-select>
          </el-form-item>
        </el-col>
      </el-row>

      <el-row>
        <el-col :span="8">
          <el-form-item label="适用专业" prop="syzy">
            <el-input v-model="dataForm.syzy" placeholder=""></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="是否分相" prop="sffx">
            <el-radio-group v-model="dataForm.sffx">
              <el-radio :label= 1>是</el-radio>
              <el-radio :label= 0>否</el-radio>
            </el-radio-group>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="是否有调度铭牌" prop="sfyddmp">
            <el-radio-group v-model="dataForm.sfyddmp">
              <el-radio :label= 1>是</el-radio>
              <el-radio :label= 0>否</el-radio>
            </el-radio-group>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="24">
          <el-form-item label="备注" prop="bz">
            <el-input v-model="dataForm.bz" placeholder=""></el-input>
          </el-form-item>
        </el-col>
      </el-row>

      <el-row>
        <el-col :span="12">
          <el-form-item label="设备类型编码_整型" prop="sblxbmZx">
            <el-input v-model="dataForm.sblxbmZx" onkeyup="this.value=this.value.replace(/[^\d.]/g,'');" placeholder=""></el-input>
          </el-form-item>
        </el-col>
<!--        <el-col :span="8">-->
<!--          <el-form-item label="设备类型名称" prop="sblxmc">-->
<!--            <el-input v-model="dataForm.sblxmc" placeholder=""></el-input>-->
<!--          </el-form-item>-->
<!--        </el-col>-->
        <el-col :span="12">
          <el-form-item label="适用专业_整型" prop="syzyZx">
            <el-input v-model="dataForm.syzyZx" onkeyup="this.value=this.value.replace(/[^\d.]/g,'');" placeholder=""></el-input>
          </el-form-item>
        </el-col>
      </el-row>

      <el-row>
        <el-col :span="8">
          <el-form-item label="是否几何类型" prop="sfjhlx">
            <el-radio-group v-model="dataForm.sfjhlx">
              <el-radio :label= 1>是</el-radio>
              <el-radio :label= 0>否</el-radio>
            </el-radio-group>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="是否容器" prop="sfrq">
            <el-radio-group v-model="dataForm.sfrq">
              <el-radio :label= 1>是</el-radio>
              <el-radio :label= 0>否</el-radio>
            </el-radio-group>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="是否挂牌" prop="sfgp">
            <el-radio-group v-model="dataForm.sfgp">
              <el-radio :label= 1>是</el-radio>
              <el-radio :label= 0>否</el-radio>
            </el-radio-group>
          </el-form-item>
        </el-col>
      </el-row>

      <el-row>
        <el-col :span="8">
          <el-form-item label="图形台帐标识" prop="txtzbs">
            <el-select  v-model="dataForm.txtzbs"  filterable     placeholder="请选择">
              <el-option
                v-for="item in txtzbList"
                :key="item.id"
                :label="item.name"
                :value="item.id">
              </el-option>
            </el-select>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="图形表名" prop="txbm">
            <el-input v-model="dataForm.txbm" placeholder=""></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="台帐表名" prop="tzbm">
            <el-input v-model="dataForm.tzbm" placeholder=""></el-input>
          </el-form-item>
        </el-col>
      </el-row>

      <el-row>
        <el-col :span="8">
          <el-form-item label="电气逻辑分类" prop="dqljfl">
            <el-input v-model="dataForm.dqljfl" placeholder=""></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="设备网络分类" prop="sbwlfl">
            <el-input v-model="dataForm.sbwlfl" placeholder=""></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="要素类集合" prop="ysljh">
            <el-input v-model="dataForm.ysljh" placeholder=""></el-input>
          </el-form-item>
        </el-col>
      </el-row>

      <el-row>
        <el-col :span="8">
          <el-form-item label="是否有附件" prop="sfyfj">
            <el-radio-group v-model="dataForm.sfyfj">
              <el-radio :label= 1>是</el-radio>
              <el-radio :label= 0>否</el-radio>
            </el-radio-group>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="电压等级下限" prop="dydjxx">
            <el-input v-model="dataForm.dydjxx" placeholder=""></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="主设备" prop="zsb">
            <el-select  v-model="dataForm.zsbid" id="zsbid"  filterable @change="changeZsb"     placeholder="请选择">
              <el-option
                v-for="item in zsbList"
                :key="item.id"
                :label="item.name"
                :value="item.id">
              </el-option>
            </el-select>
          </el-form-item>
        </el-col>
        <el-form-item style="display: none" prop="zsbmc">
          <el-input v-model="dataForm.zsbmc"></el-input>
        </el-form-item>
      </el-row>

      <el-row>
        <el-col :span="8">
          <el-form-item label="计量单位" prop="jldw">
            <el-input v-model="dataForm.jldw" placeholder=""></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="是否启用" prop="sfqy">
            <el-radio-group v-model="dataForm.sfqy">
              <el-radio :label= 1>是</el-radio>
              <el-radio :label= 0>否</el-radio>
            </el-radio-group>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="是否总部维护型号" prop="sfzbwhxh">
            <el-radio-group v-model="dataForm.sfzbwhxh">
              <el-radio :label= 1>是</el-radio>
              <el-radio :label= 0>否</el-radio>
            </el-radio-group>
          </el-form-item>
        </el-col>
      </el-row>

      <el-row>
        <el-col :span="8">
          <el-form-item label="是否为首次同步OMS类型" prop="sctboms">
            <el-radio-group v-model="dataForm.sctboms">
              <el-radio :label= 1>是</el-radio>
              <el-radio :label= 0>否</el-radio>
            </el-radio-group>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="同步OMS条件" prop="omsfilter">
            <el-input v-model="dataForm.omsfilter" placeholder=""></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="是否同步台账" prop="sftbtz">
            <el-radio-group v-model="dataForm.sftbtz">
              <el-radio :label= 1>是</el-radio>
              <el-radio :label= 0>否</el-radio>
            </el-radio-group>
          </el-form-item>
        </el-col>
      </el-row>

      <el-row>
        <el-col :span="8">
          <el-form-item label="排序字段2" prop="pxzd2">
            <el-input v-model="dataForm.pxzd2" placeholder=""></el-input>
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
      zsb: {id: '11118000000121', name: '主变A相'},
      visible: false,
      dataForm: {
        objId: 0,
        sblxbm: '',
        sblx: '',
        fsblxid: '',
        syzy: '',
        sffx: '',
        sfyddmp: '',
        bz: '',
        sblxbmZx: '',
            // sblxmc: '',
        syzyZx: '',
        sfjhlx: '',
        sfrq: '',
        sfgp: '',
        txtzbs: '',
        txbm: '',
        tzbm: '',
        dqljfl: '',
        sbwlfl: '',
        ysljh: '',
        sfyfj: '',
        dydjxx: '',
        zsbid: '',
        zsbmc: '',
        jldw: '',
        sfqy: '',
        sfzbwhxh: '',
        sctboms: '',
        omsfilter: '',
        sftbtz: '',
        pxzd2: ''
      },
      dataRule: {
            // name: [
            //             { required: true, message: '电压等级名称不能为空', trigger: 'blur' }
            // ]
      },
      txtzbList: [{
        id: 0,
        name: '无'
      }, {
        id: 1,
        name: '图形'
      }, {
        id: 2,
        name: '台账'
      }, {
        id: 3,
        name: '图形和台账'
      }],
      zsbList: [ {
        id: '11118000439520',
        name: '主变'
      }, {
        id: '11118000000121',
        name: '主变A相'
      }, {
        id: '11118000000151',
        name: '主变B相'
      }, {
        id: '11118000003651',
        name: '主变C相'
      }],
      fsblxList: [ {
        id: '809A5581-89D5-475B-9BCE-808FC04CB767-00101',
        name: '架空线路设备'
      }, {
        id: '16A5C924-A478-4130-8E0D-9AC0A47D4FD0-41111',
        name: '设备所属大馈线'
      }]
    }
  },
  components: {
  },
  methods: {
    changeZsb (val) {
      let zsbList = this.zsbList
      if (zsbList.length > 0) {
        for (const i in this.zsbList) {
          if (zsbList[i].id === val) {
            this.dataForm.zsbmc = zsbList[i].name
          }
        }
      }
    },
    init (id) {
      this.visible = true
      this.dataForm.objId = id || 0
      if (id !== undefined && id !== 0) {
        this.$http({
          url: this.$http.adornUrl(`/standing/parts-type/GET/partsType`),
          method: 'get',
          params: this.$http.adornParams({
            'objId': id
          })
        }).then(({data}) => {
          if (data.status === 'success') {
            this.dataForm.objId = data.rows[0].objId
            this.dataForm.sblxbm = data.rows[0].sblxbm
            this.dataForm.sblx = data.rows[0].sblx
            this.dataForm.fsblxid = data.rows[0].fsblxid
            this.dataForm.syzy = data.rows[0].syzy
            this.dataForm.sffx = data.rows[0].sffx
            this.dataForm.sfyddmp = data.rows[0].sfyddmp
            this.dataForm.bz = data.rows[0].bz
            this.dataForm.sblxbmZx = data.rows[0].sblxbmZx
                // this.dataForm.sblxmc = data.rows[0].sblxmc
            this.dataForm.syzyZx = data.rows[0].syzyZx
            this.dataForm.sfjhlx = data.rows[0].sfjhlx
            this.dataForm.sfrq = data.rows[0].sfrq
            this.dataForm.sfgp = data.rows[0].sfgp
            this.dataForm.txtzbs = data.rows[0].txtzbs
            this.dataForm.txbm = data.rows[0].txbm
            this.dataForm.tzbm = data.rows[0].tzbm
            this.dataForm.dqljfl = data.rows[0].dqljfl
            this.dataForm.sbwlfl = data.rows[0].sbwlfl
            this.dataForm.ysljh = data.rows[0].ysljh
            this.dataForm.sfyfj = data.rows[0].sfyfj
            this.dataForm.dydjxx = data.rows[0].dydjxx
            this.dataForm.zsbid = data.rows[0].zsbid
            this.dataForm.zsbmc = data.rows[0].zsbmc
            this.dataForm.jldw = data.rows[0].jldw
            this.dataForm.sfqy = data.rows[0].sfqy
            this.dataForm.sfzbwhxh = data.rows[0].sfzbwhxh
            this.dataForm.sctboms = data.rows[0].sctboms
            this.dataForm.omsfilter = data.rows[0].omsfilter
            this.dataForm.sftbtz = data.rows[0].sftbtz
            this.dataForm.pxzd2 = data.rows[0].pxzd2
            this.zsb.id = data.rows[0].zsbid
            this.zsb.name = data.rows[0].zsbmc
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
              url: this.$http.adornUrl(`/standing/parts-type/PUT/partsType`),
              method: 'put',
              data: this.$http.adornData({
                'objId': objId,
                'sblxbm': this.dataForm.sblxbm,
                'sblx': this.dataForm.sblx,
                'fsblxid': this.dataForm.fsblxid,
                'syzy': this.dataForm.syzy,
                'sffx': this.dataForm.sffx,
                'sfyddmp': this.dataForm.sfyddmp,
                'bz': this.dataForm.bz,
                'sblxbmZx': this.dataForm.sblxbmZx,
                'syzyZx': this.dataForm.syzyZx,
                'sfjhlx': this.dataForm.sfjhlx,
                'sfrq': this.dataForm.sfrq,
                'sfgp': this.dataForm.sfgp,
                'txtzbs': this.dataForm.txtzbs,
                'txbm': this.dataForm.txbm,
                'tzbm': this.dataForm.tzbm,
                'dqljfl': this.dataForm.dqljfl,
                'sbwlfl': this.dataForm.sbwlfl,
                'ysljh': this.dataForm.ysljh,
                'sfyfj': this.dataForm.sfyfj,
                'dydjxx': this.dataForm.dydjxx,
                'zsbid': this.dataForm.zsbid,
                'zsbmc': this.dataForm.zsbmc,
                'jldw': this.dataForm.jldw,
                'sfqy': this.dataForm.sfqy,
                'sfzbwhxh': this.dataForm.sfzbwhxh,
                'sctboms': this.dataForm.sctboms,
                'omsfilter': this.dataForm.omsfilter,
                'sftbtz': this.dataForm.sftbtz,
                'pxzd2': this.dataForm.pxzd2
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
              url: this.$http.adornUrl(`/standing/parts-type/POST/partsType`),
              method: 'post',
              data: this.$http.adornData({
                'sblxbm': this.dataForm.sblxbm,
                'sblx': this.dataForm.sblx,
                'fsblxid': this.dataForm.fsblxid,
                'syzy': this.dataForm.syzy,
                'sffx': this.dataForm.sffx,
                'sfyddmp': this.dataForm.sfyddmp,
                'bz': this.dataForm.bz,
                'sblxbmZx': this.dataForm.sblxbmZx,
                    // 'sblxmc': this.dataForm.sblxmc,
                'syzyZx': this.dataForm.syzyZx,
                'sfjhlx': this.dataForm.sfjhlx,
                'sfrq': this.dataForm.sfrq,
                'sfgp': this.dataForm.sfgp,
                'txtzbs': this.dataForm.txtzbs,
                'txbm': this.dataForm.txbm,
                'tzbm': this.dataForm.tzbm,
                'dqljfl': this.dataForm.dqljfl,
                'sbwlfl': this.dataForm.sbwlfl,
                'ysljh': this.dataForm.ysljh,
                'sfyfj': this.dataForm.sfyfj,
                'dydjxx': this.dataForm.dydjxx,
                'zsbid': this.dataForm.zsbid,
                'zsbmc': this.dataForm.zsbmc,
                'jldw': this.dataForm.jldw,
                'sfqy': this.dataForm.sfqy,
                'sfzbwhxh': this.dataForm.sfzbwhxh,
                'sctboms': this.dataForm.sctboms,
                'omsfilter': this.dataForm.omsfilter,
                'sftbtz': this.dataForm.sftbtz,
                'pxzd2': this.dataForm.pxzd2
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
