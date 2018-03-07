<template>
<div class="login-box">
  <el-form label-position="left" :model="loginForm" ref="loginForm" :rules="loginRules" @keyup.enter.native="validate">
    <p class="app-name">Api Mocker</p>
    <el-form-item prop="email">
      <el-input placeholder="email" v-model="loginForm.email" ></el-input>
    </el-form-item>
    <el-form-item prop="password">
      <el-input type="password" placeholder="password" v-model="loginForm.password"></el-input>
    </el-form-item>
    <el-form-item class="control">
      <el-button type="primary" @click="validate">Login</el-button>
      <router-link class="reset-pass" to="find-pass">Forgot your password</router-link>
      <router-link class="register" to="register">--> Register</router-link>
    </el-form-item>
  </el-form>
</div>
</template>

<script>
export default {
  data () {
    return {
      loginRules: {
        email: [{
          type: 'email',
          required: true,
          trigger: 'blur'
        }],
        password: [{
          required: true,
          trigger: 'blur'
        }]
      },
      loginForm: {
        email: '',
        password: ''
      }
    }
  },
  methods: {
    validate () {
      this.$refs.loginForm.validate(rs => {
        if (rs) {
          this.login()
        }
      })
    },
    login () {
      this.$store.dispatch('login', this.loginForm).then(() => {
        this.$message.success('Success')
        window.setTimeout(() => {
          this.$router.push({
            name: 'AllList'
          })
        }, 1000)
      }).catch(err => this.$message.error(`Failed：${err.msg}`))
    }
  }
}
</script>
<style>
</style>
