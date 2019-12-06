<template>
  <v-container fluid fill-height>
    <v-container fluid fill-height>
      <v-layout align-center justify-center>
        <v-flex xs12 sm8 md4 lg4>
          <v-card>
            <Notification :message="error" v-if="error" />
          </v-card>
          <v-card class="elevation-1 pa-3">
            <v-card-text>
              <div class="layout column align-center">
                <h1 class="flex my-4 primary--text">Register</h1>
              </div>
              <v-form @submit.prevent="register" method="post">
                <v-text-field
                  v-model="username"
                  :rules="nameRules"
                  :counter="10"
                  label="Username"
                  required
                ></v-text-field>

                <v-text-field
                  v-model="email"
                  :rules="emailRules"
                  label="E-mail"
                  required
                ></v-text-field>

                <v-text-field
                  v-model="password"
                  @click:append="showPw = !showPw"
                  :append-icon="showPw ? 'mdi-eye' : 'mdi-eye-off'"
                  :rules="[rules.required, rules.min]"
                  :type="showPw ? 'text' : 'password'"
                  name="email"
                  label="Password"
                  hint="At least 8 characters"
                  counter
                >
                </v-text-field>
              </v-form>
            </v-card-text>
            <v-card-actions>
              <v-btn @click="register" block color="primary">Register</v-btn>
            </v-card-actions>
            <div class="has-text-centered" style="margin-top: 20px">
              Already got an account? <nuxt-link to="/login">Login</nuxt-link>
            </div>
          </v-card>
        </v-flex>
      </v-layout>
    </v-container>
  </v-container>
</template>

<script>
import Notification from '~/components/Notification'

export default {
  components: {
    Notification
  },

  data() {
    return {
      username: '',
      email: '',
      password: '',
      error: null,
      showPw: false,
      nameRules: [(v) => !!v || 'The input is required'],
      emailRules: [
        (v) => !!v || 'E-mail is required',
        (v) => /.+@.+/.test(v) || 'E-mail must be valid'
      ],
      rules: {
        required: (value) => !!value || 'Required.',
        min: (v) => v.length >= 8 || 'Min 8 characters',
        emailMatch: () => "The email and password you entered don't match"
      }
    }
  },

  methods: {
    async register() {
      try {
        await this.$axios.post('register', {
          username: this.username,
          email: this.email,
          password: this.password
        })

        await this.$auth.loginWith('local', {
          data: {
            email: this.email,
            password: this.password
          }
        })

        this.$router.push('/')
      } catch (e) {
        this.error = e.response.data.message
      }
    }
  }
}
</script>
