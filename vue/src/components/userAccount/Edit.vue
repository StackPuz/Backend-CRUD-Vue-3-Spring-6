<template>
  <div class="container">
    <div class="row">
      <div class="col">
        <form method="post" @submit.prevent="edit()">
          <div class="row">
            <div class="form-group col-md-6 col-lg-4">
              <label for="user_account_id">Id</label>
              <input readonly id="user_account_id" name="id" class="form-control form-control-sm" v-model="userAccount.id" type="number" required />
              <span v-if="errors.id" class="text-danger">{{errors.id}}</span>
            </div>
            <div class="form-group col-md-6 col-lg-4">
              <label for="user_account_name">Name</label>
              <input id="user_account_name" name="name" class="form-control form-control-sm" v-model="userAccount.name" required maxlength="50" />
              <span v-if="errors.name" class="text-danger">{{errors.name}}</span>
            </div>
            <div class="form-group col-md-6 col-lg-4">
              <label for="user_account_email">Email</label>
              <input id="user_account_email" name="email" class="form-control form-control-sm" v-model="userAccount.email" type="email" required maxlength="50" />
              <span v-if="errors.email" class="text-danger">{{errors.email}}</span>
            </div>
            <div class="form-group col-md-6 col-lg-4">
              <label for="user_account_password">Password</label>
              <input id="user_account_password" name="password" class="form-control form-control-sm" v-model="userAccount.password" type="password" placeholder="New password" maxlength="100" />
              <span v-if="errors.password" class="text-danger">{{errors.password}}</span>
            </div>
            <div class="form-group col-md-6 col-lg-4">
              <label for="user_account_password2">Confirm password</label>
              <input data-match="user_account_password" id="user_account_password2" name="password2" class="form-control form-control-sm" type="password" placeholder="New password" maxlength="100" />
              <span v-if="errors.password" class="text-danger">{{errors.password}}</span>
            </div>
            <div class="form-check col-md-6 col-lg-4">
              <input id="user_account_active" name="active" class="form-check-input" type="checkbox" v-model="userAccount.active" :checked="userAccount.active" />
              <label class="form-check-label" for="user_account_active">Active</label>
              <span v-if="errors.active" class="text-danger">{{errors.active}}</span>
            </div>
            <div class="col-12">
              <h6>
              </h6><label>Roles</label>
              <div v-for="role in roles" :key="role" class="form-check">
                <input :id="`user_role_role_id${role.id}`" name="roleId" class="form-check-input" type="checkbox" :value="role.id" :checked="userAccountUserRoles.some(e => e.id.roleId == role.id)" />
                <label class="form-check-label" :for="`user_role_role_id${role.id}`">{{role.name}}</label>
              </div>
            </div>
            <div class="col-12">
              <router-link class="btn btn-sm btn-secondary" :to="getRef('/userAccount')">Cancel</router-link>
              <button class="btn btn-sm btn-primary">Submit</button>
            </div>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>
<script>
import Service from './Service'
import Util from"../../util"

export default {
  name: 'UserAccountEdit',
  data() {
    return {
      userAccount: {},
      userAccountUserRoles: [],
      roles: [],
      errors: {}
    }
  },
  mounted() {
    this.get().finally(() => {
      this.initView(true)
    })
  },
  methods: {
    ...Util,
    get() {
      return Service.edit(this.$route.params.id).then(response => {
        this.userAccount = response.data.userAccount
        this.userAccountUserRoles = response.data.userAccountUserRoles
        this.roles = response.data.roles
      })
    },
    edit() {
      if (!this.validateForm()) {
        return
      }
      let data = { ...this.userAccount }
      data.roleId = Array.from(document.querySelectorAll('[name="roleId"]:checked')).map(e => e.value)
      data = Util.getFormData(data)
      Service.edit(this.$route.params.id, data).then(() => {
        this.$router.push(this.getRef('/userAccount'))
      }).catch((e) => {
        if (e.response.data.errors) {
          this.errors = e.response.data.errors
        }
        else {
          alert(e.response.data.message)
        }
      })
    }
  }
}
</script>
