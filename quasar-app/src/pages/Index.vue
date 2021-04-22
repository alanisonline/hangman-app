<template>
  <q-page class="row items-center justify-center">
    <div v-if="user" full-width>
      <p>
        USER:
        {{
          this.user.hasOwnProperty('name')
            ? $emit('user-name', this.user.name) && this.user.name
            : ''
        }}
      </p>
      <GameComponent />
    </div>
    <div v-else>
      <div class="column">
        <div class="row">
          <h5 class="text-h5 text-grey-8 q-ma-md">Login</h5>
        </div>
        <q-card square bordered class="q-pa-lg shadow-1">
          <q-card-section class="form-card-section">
            <q-form
              class="q-gutter-md full-width"
              method="POST"
              @submit="handleLogin"
              action="/login"
            >
              <q-input
                square
                class="full-width"
                v-model="login.email"
                type="email"
                label="email"
                :rules="[(val) => !!val || 'Field is required']"
                lazy-rules
              />
              <q-input
                square
                filled
                clearable
                class="full-width"
                v-model="login.password"
                type="password"
                label="password"
                :rules="[(val) => !!val || 'Field is required']"
                lazy-rules
              />
              <q-btn
                unelevated
                color="light-green-7"
                size="lg"
                class="full-width"
                label="Login"
                type="submit"
              />
            </q-form>
            <q-banner
              v-show="loginError.enabled"
              dense
              inline-actions
              class="text-white bg-red"
            >
              {{ loginError.message }}
              <template v-slot:action>
                <q-btn flat color="white" label="Login failed" />
              </template>
            </q-banner>
          </q-card-section>
          <q-card-section hide class="text-center">
            <p class="text-grey-6">
              Not registered?
              <q-btn flat class="text-blue-3">Create an Account</q-btn>
            </p>
          </q-card-section>
        </q-card>
      </div>
    </div>
  </q-page>
</template>

<script lang="ts">
import Vue from 'vue';
import axios, { AxiosError, AxiosResponse } from 'axios';
import GameComponent from '../components/GameComponent.vue';

export default Vue.extend({
  name: 'PageIndex',
  components: { GameComponent },
  data(): {
    user: unknown;
    login: { email: string; password: string };
    loginError: { enabled: boolean; message: string };
  } {
    return {
      user: undefined,
      login: { email: '', password: '' },
      loginError: { enabled: false, message: '' },
    };
  },
  mounted() {
    void axios
      .get('/sanctum/csrf-cookie')
      .catch((err) =>
        console.log('There has been an error obtaining a session cookie', err)
      )
      .then((): void => {
        this.getUser(false);
      });
  },
  methods: {
    getUser(displayError = true): string {
      let errorOutput = '';
      void axios
        .get('api/user')
        .then((response): void => {
          this.user = <AxiosResponse>response.data;
        })
        .catch((err: AxiosError): void => {
          if (displayError) {
            this.loginError.enabled = displayError;
            this.loginError.message =
              err.response !== undefined
                ? err.response.statusText
                : 'Error undefined.';
          }
        });
      return errorOutput;
    },
    handleLogin() {
      void axios
        .post('/login', this.login)
        .then((): void => {
          this.getUser();
          console.log('loginError: ' + this.loginError.message);
        })
        .catch(
          (err: AxiosError): string =>
            (this.loginError.message =
              err.response !== undefined
                ? err.response.statusText
                : 'Error undefined.')
        );
    },
  },
});
</script>

<style lang="scss">
.form-card-section {
  min-width: 320px;
}
</style>