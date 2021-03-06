<template>
  <v-main>
    <v-container class="fill-height" fluid>
      <v-row align="center" justify="center">
        <v-col cols="12" sm="8" md="4">
          <v-card class="elevation-12">
            <ValidationObserver ref="observer" v-slot="{ handleSubmit }">
              <v-form @submit.prevent="handleSubmit(onSubmit)">
                <v-toolbar color="primary" dark flat>
                  <v-toolbar-title>Create an account</v-toolbar-title>
                  <v-spacer></v-spacer>
                </v-toolbar>
                <v-card-text>
                  <div class="text-left pb-6">
                    Already have an account?
                    <router-link to="/login">Sign in</router-link>
                  </div>

                  <ValidationProvider
                    v-slot="{ errors }"
                    name="Email"
                    rules="required|email"
                  >
                    <v-text-field
                      v-model="email"
                      :error-messages="errors"
                      :aria-describedby="errors"
                      hint="example@domain.com"
                      label="Email"
                      name="email"
                      required
                    ></v-text-field>
                  </ValidationProvider>
                  <v-row>
                    <v-col cols="12" md="6">
                      <ValidationProvider
                        v-slot="{ errors }"
                        name="First Name"
                        rules="required"
                      >
                        <v-text-field
                          v-model="firstName"
                          :error-messages="errors"
                          :aria-describedby="errors"
                          label="First name"
                          required
                        ></v-text-field>
                      </ValidationProvider>
                    </v-col>
                    <v-col cols="12" md="6">
                      <ValidationProvider
                        v-slot="{ errors }"
                        name="Last Name"
                        rules="required"
                      >
                        <v-text-field
                          v-model="lastName"
                          :error-messages="errors"
                          :aria-describedby="errors"
                          label="Last name"
                          required
                        ></v-text-field>
                      </ValidationProvider>
                    </v-col>
                  </v-row>
                  <!-- Add Show Password button -->
                  <ValidationProvider
                    v-slot="{ errors }"
                    name="Password"
                    rules="required|min:6"
                  >
                    <v-text-field
                      v-model="password"
                      :error-messages="errors"
                      :aria-describedby="errors"
                      label="Password"
                      name="new-password"
                      hint="At least 6 characters"
                      :append-icon="showPassword ? 'mdi-eye' : 'mdi-eye-off'"
                      :type="showPassword ? 'text' : 'password'"
                      @click:append="showPassword = !showPassword"
                    ></v-text-field>
                  </ValidationProvider>
                  <ValidationProvider
                    v-slot="{ errors }"
                    name="Country"
                    rules="required"
                  >
                    <v-select
                      v-model="country"
                      :error-messages="errors"
                      :aria-describedby="errors"
                      :items="items"
                      :rules="[v => !!v || 'Country/Region field is required']"
                      label="Country/Region"
                      required
                    ></v-select>
                  </ValidationProvider>
                </v-card-text>
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="primary" type="submit">Create account</v-btn>
                </v-card-actions>
              </v-form>
            </ValidationObserver>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
  </v-main>
</template>

<script>
import { mapState, mapActions } from 'vuex'

import { required, email, min } from 'vee-validate/dist/rules'
import {
  extend,
  ValidationObserver,
  ValidationProvider,
  setInteractionMode
} from 'vee-validate'

// import { EventBus } from './Alert/BaseAlertComponent'

setInteractionMode('eager')

extend('required', {
  ...required,
  message: '{_field_} can not be empty'
})

extend('min', {
  ...min,
  message: '{_field_} must contain at least {length} characters.'
})

extend('email', {
  ...email,
  message: 'Email must be valid'
})

export default {
  name: 'SignUpComponent',
  components: {
    ValidationObserver,
    ValidationProvider
  },
  data: () => ({
    email: '',
    password: '',
    showPassword: '',
    firstName: '',
    lastName: '',
    country: 'Estonia',
    items: ['United States', 'Estonia', 'Canada']
  }),
  methods: {
    ...mapActions('account', ['register']),
    async onSubmit () {
      const { email, fullName, password, country } = this

      // const formDataQuery = {
      //   query: `
      //     mutation {
      //       createUser(userInput: {email: "${this.email}", name:"${this.fullName}", password:"${this.password}", country: "${this.country}"}) {
      //         _id
      //         email
      //       }
      //     }
      //   `
      // }

      await this.register({ email, password, fullName, country })
    }
  },
  computed: {
    fullName () {
      return this.firstName + ' ' + this.lastName
    },
    ...mapState('account', ['status'])
  }
}
</script>

<style></style>
