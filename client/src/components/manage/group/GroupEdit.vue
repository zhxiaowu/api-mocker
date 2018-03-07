<template>
<el-dialog :title="title" :visible="visible" @open="initLocalGroup" :show-close="false">
  <el-form v-stop-default-enter label-position="right" label-width="84px">
    <el-form-item label="Group name：">
      <el-input v-model="localGroup.name"></el-input>
    </el-form-item>
    <el-form-item label="Privacy:">
      <el-radio-group v-model="localGroup.privacy">
        <el-radio :label="0">Everyone</el-radio>
        <el-radio :label="1">Group members</el-radio>
        <el-radio :label="3">Private</el-radio>
      </el-radio-group>
    </el-form-item>
    <el-form-item label="Permissions:">
      <el-radio-group v-model="localGroup.operation">
        <el-radio :label="0">Everyone</el-radio>
        <el-radio :label="1">Group members</el-radio>
      </el-radio-group>
    </el-form-item>
    <el-form-item label="Member:">
      <user-selector
        class="group-member"
        :value="localGroup.member"
        :remoteMethod="searchUsers"
        :options="users"
        @itemRemove="itemRemove"
        @itemClick="itemClick">
      </user-selector>
    </el-form-item>
  </el-form>
  <div slot="footer" class="dialog-footer">
    <el-button @click="cancel">Cancel</el-button>
    <el-button type="primary" @click="confirm">Enter</el-button>
  </div>
</el-dialog>
</template>

<script>
import UserSelector from '@/components/common/UserSelector'
import R from 'ramda'
export default {
  components: {
    UserSelector
  },
  props: {
    group: Object,
    visible: Boolean
  },
  data () {
    return {
      localGroup: R.clone(this.group),
      users: []
    }
  },
  mounted () {
    this.initUsers()
  },
  computed: {
    allUsers () {
      return this.$store.state.allUsers
    },
    title () {
      return `${this.localGroup.name}`
    }
  },
  methods: {
    initUsers () {
      this.users = this.localGroup.member.map(id =>
        this.allUsers.find(u => u._id === id)
      )
    },
    searchUsers (val) {
      this.users = this.allUsers.filter(u =>
        u.email.indexOf(val) >= 0 || u.name.indexOf(val) >= 0
      )
    },
    itemClick (val) {
      this.localGroup.member = R.symmetricDifference(this.localGroup.member, [val])
    },
    itemRemove (val) {
      this.localGroup.member = R.without([val], this.localGroup.member)
    },
    initLocalGroup () {
      this.localGroup = R.clone(this.group)
      this.initUsers()
    },
    cancel () {
      this.$emit('hide')
    },
    confirm () {
      this.$store.dispatch('updateGroup', this.localGroup).then(rs => {
        this.$message.success('Success')
        this.$emit('update', rs.data)
        this.$emit('hide')
      }).catch(err => this.$message.error(err.msg))
    }
  }
}
</script>
