<template>
  <el-form-item label="Password" class="profile-item profile-password">
    <el-button type="text" @click="dialogVisible = true">修改密码</el-button>
    <el-dialog title="Change password"
               v-model="dialogVisible"
               size="tiny"
               :show-close="false"
               class="update-password"
               @close="handleDialogClose">
      <el-form label-position="left" :model="passwordForm" :rules="passwordRules" ref="passwordForm">
        <el-form-item prop="originPassword">
          <el-input type="password"
                    auto-complete="off"
                    placeholder="Please enter the original password"
                    v-model="passwordForm.originPassword"></el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input type="password"
                    auto-complete="off"
                    placeholder="Please enter a new password"
                    v-model="passwordForm.password"></el-input>
        </el-form-item>
        <el-form-item prop="verifyPassword">
          <el-input type="password"
                    auto-complete="off"
                    placeholder="Please confirm the new password"
                    v-model="passwordForm.verifyPassword"></el-input>
        </el-form-item>
      </el-form>
      <div class="dialog-footer" slot="footer">
        <el-button type="default" @click="handleCancel">Cancel</el-button>
        <el-button type="primary" @click="handleSave" :disabled="disableSave">Save</el-button>
      </div>
    </el-dialog>
  </el-form-item>
</template>
<script>
export default {
  data () {
    return {
      dialogVisible: false,
      passwordRules: {
        originPassword: [{
          required: true,
          trigger: 'blur'
        }],
        password: [{
          validator: this.validateNewPass,
          required: true,
          trigger: 'blur'
        }],
        verifyPassword: [{
          validator: this.validateVerifyPass,
          trigger: 'blur'
        }]
      },
      passwordForm: {
        originPassword: '',
        password: '',
        verifyPassword: ''
      }
    }
  },
  methods: {
    validateNewPass (rule, value, callback) {
      if (value === this.passwordForm.originPassword) {
        callback(new Error('new password cannot equal to old password'))
      } else {
        callback()
      }
    },
    validateVerifyPass (rule, value, callback) {
      if (value === '') {
        callback(new Error('verify password is required'))
      } else if (value !== this.passwordForm.password) {
        callback(new Error('verify password don\'t match'))
      } else {
        callback()
      }
    },
    handleSave () {
      this.$refs.passwordForm.validate(rs => {
        if (rs) this.updatePassword()
      })
    },
    updatePassword () {
      this.$store.dispatch('updatePassword', this.passwordForm).then(() => {
        this.$message.success('Success')
        this.dialogVisible = false
      }).catch(err => this.$message.error(err.msg))
    },
    handleCancel () {
      this.dialogVisible = false
    },
    handleDialogClose () {
      this.$refs.passwordForm.resetFields()
    }
  },
  computed: {
    disableSave () {
      const { originPassword, password, verifyPassword } = this.passwordForm
      return originPassword.trim() === '' || password.trim() === '' || verifyPassword.trim() === ''
    }
  }
}
</script>
<style lang="less">
.profile-item.profile-password {
  .el-form-item {
    margin-bottom: 20px;
  }
  .el-input {
    width: 100%;
  }
}
</style>
