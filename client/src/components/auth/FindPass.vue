<template>
  <div class="login-box reset-password">
    <el-form label-position="left" :model="findForm" :rules="resetRules" ref="findForm" v-stop-default-enter>
      <p class="app-name">Reset password</p>
      <el-form-item prop="email">
        <el-input placeholder="email" v-model="findForm.email"></el-input>
      </el-form-item>
      <el-form-item class="control">
        <el-button type="primary" @click="validate">Send</el-button>
        <router-link class="login" to="login">--> Login</router-link>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
import { mapActions } from 'vuex'
export default {
  data () {
    return {
      resetRules: {
        email: [{
          type: 'email',
          required: true,
          trigger: 'blur'
        }]
      },
      findForm: {
        email: ''
      }
    }
  },
  methods: {
    ...mapActions(['sendResetPassTicket']),
    validate () {
      if (this.sentCodeToken) {
        return
      }
      this.$refs.findForm.validateField('email', rs => {
        if (rs) {
          this.$message.error('email is not a valid email')
          return
        }
        this.sentCodeToken = true
        this.sendResetPassTicket(this.findForm.email).then(() => {
          this.$message.info('Send success, please check the email.')
          this.sentCodeToken = false
        }).catch(err => {
          this.$message.error(`Send failed：${err.msg}`)
          this.sentCodeToken = false
        })
      })
    }
  }
}
</script>
<style>
</style>
