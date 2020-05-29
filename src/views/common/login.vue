<template>
  <div class="site-wrapper site-page--login">
    <div class="site-content__wrapper">
      <div class="site-content">
        <div class="brand-info">
          <!--<h2 class="brand-info__text">renren-fast-vue</h2>-->
          <!--<p class="brand-info__intro">renren-fast-vue基于vue、element-ui构建开发，实现renren-fast后台管理前端功能，提供一套更优的前端解决方案。</p>-->
        </div>
        <div class="login-main">
          <h3 class="login-title">管理员登录</h3>
          <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" status-icon>
            <el-form-item prop="username">
              <el-input v-model="dataForm.username" placeholder="用户名"></el-input>
            </el-form-item>
            <el-form-item prop="password">
              <el-input v-model="dataForm.password" type="password" placeholder="密码"></el-input>
            </el-form-item>
            <el-form-item>
              <el-button class="login-btn-submit" type="primary" @click="dataFormSubmit()">登录</el-button>
            </el-form-item>
          </el-form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import { getUUID } from '@/utils'
  import Vue from 'vue'
  export default {
    data () {
      return {
        dataForm: {
          userName: '',
          password: ''

        },
        dataRule: {
          username: [
            { required: true, message: '用户名', trigger: 'blur' }
          ],
          password: [
            { required: true, message: '密码不能为空', trigger: 'blur' }
          ]

        },
        captchaPath: ''
      }
    },
    methods: {
      // 提交表单
      dataFormSubmit () {
        let username = this.dataForm.username
        let reg = /^\s*$/g
        if (!reg.test(username) && username != null && username != "") {
          this.$refs['dataForm'].validate((valid) => {
            if (valid) {
              this.$http({
                url: this.$http.adornUrl('/auth/web/passWord/login'),
                method: 'post',
                data: this.$http.adornData({
                  'username': this.dataForm.username,
                  'password': this.dataForm.password
                })
              }).then(({data}) => {
                if (data.status === 'success') {
                  this.$cookie.set('Authorization', data.rows.headerValue)
                  this.$router.replace({ name: 'home' })
                } else {
                  this.getCaptcha()
                  this.$message.error(data.message)
                }
              })
            }
          })
        } else {
          this.$message.error('用户名不能为空')
        }
      },
      // 获取验证码
      getCaptcha () {
        this.dataForm.uuid = getUUID()
        this.captchaPath = this.$http.adornUrl(`/captcha.jpg?uuid=${this.dataForm.uuid}`)
      }
    }
  }
</script>

<style lang="scss">
  .site-wrapper.site-page--login {
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    background-color: rgba(38, 50, 56, .6);
    overflow: hidden;
    &:before {
      position: fixed;
      top: 0;
      left: 0;
      z-index: -1;
      width: 100%;
      height: 100%;
      content: "";
      background-image: url(~@/assets/img/login_bg.jpg);
      background-size: cover;
    }
    .site-content__wrapper {
      position: absolute;
      top: 0;
      right: 0;
      bottom: 0;
      left: 0;
      padding: 0;
      margin: 0;
      overflow-x: hidden;
      overflow-y: auto;
      background-color: transparent;
    }
    .site-content {
      min-height: 100%;
      padding: 30px 500px 30px 30px;
    }
    .brand-info {
      margin: 220px 100px 0 90px;
      color: #fff;
    }
    .brand-info__text {
      margin:  0 0 22px 0;
      font-size: 48px;
      font-weight: 400;
      text-transform : uppercase;
    }
    .brand-info__intro {
      margin: 10px 0;
      font-size: 16px;
      line-height: 1.58;
      opacity: .6;
    }
    /*.login-main {*/
      /*position: absolute;*/
      /*top: 0;*/
      /*right: 0;*/
      /*padding: 150px 60px 180px;*/
      /*width: 470px;*/
      /*min-height: 100%;*/
      /*background-color: #fff;*/
    /*}*/
    .login-main {
      position: absolute;
      top: 30%;
      left: 36%;
      padding: 15px 60px 180px;
      width: 470px;
      height: 300px;
      background-color: #fff;
    }
    .login-title {
      font-size: 16px;
    }
    .login-captcha {
      overflow: hidden;
      > img {
        width: 100%;
        cursor: pointer;
      }
    }
    .login-btn-submit {
      width: 100%;
      margin-top: 38px;
    }
  }
</style>
