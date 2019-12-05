<template>
  <v-template>
    <v-template v-if="isAuthenticated">
      <v-menu offset-y>
        <v-template v-slot:activator="{ on }">
          <v-btn v-on="on" text>
            {{ loggedInUser.username }}
            <v-icon right>mdi-account-circle</v-icon>
          </v-btn>
        </v-template>
        <v-list>
          <v-list-item>
            <v-btn text to="/">
              <v-icon left>mdi-account-outline</v-icon>
              Nuxt Auth
            </v-btn>
          </v-list-item>
          <v-list-item>
            <v-btn text to="/profile">
              <v-icon left>mdi-face-profile</v-icon>
              My Profile
            </v-btn>
          </v-list-item>
          <v-list-item>
            <v-btn @click="logout" text>
              <v-icon left>mdi-logout</v-icon>
              Logout
            </v-btn>
          </v-list-item>
        </v-list>
      </v-menu>
    </v-template>
    <v-template v-else>
      <v-btn text to="/register">
        <v-icon left>mdi-account-plus</v-icon>
        Register
      </v-btn>
      <v-btn text to="/login">
        <v-icon left>mdi-login-variant</v-icon>
        Log In
      </v-btn>
    </v-template>
  </v-template>
</template>

<script>
import { mapGetters } from 'vuex'

export default {
  name: 'NavbarHeader',

  computed: {
    ...mapGetters(['isAuthenticated', 'loggedInUser'])
  },

  methods: {
    async logout() {
      await this.$auth.logout()
    }
  }
}
</script>
