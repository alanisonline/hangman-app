<template>
  <q-page class="row items-center justify-center">
    <div v-if="user" full-width>
      <p>USER: {{ this.user.hasOwnProperty('name') ? this.user.name : '' }}</p>
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
              />
              <q-input
                square
                filled
                clearable
                class="full-width"
                v-model="login.password"
                type="password"
                label="password"
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
          </q-card-section>
          <q-card-section hide class="text-center">
            <p class="text-grey-6">
              Not registered?
              <q-btn flat class="text-blue-3" v-on:click="$emit('register')"
                >Create an Account</q-btn
              >
            </p>
          </q-card-section>
        </q-card>
      </div>
    </div>
  </q-page>
</template>

<script lang="ts">
import Vue from 'vue';
import axios from 'axios';
import GameComponent from '../components/GameComponent.vue';

export default Vue.extend({
  name: 'PageIndex',
  components: { GameComponent },
  data() {
    return {
      user: <unknown | null>null,
      login: { email: '', password: '' },
    };
  },
  mounted() {
    void axios
      .get('/sanctum/csrf-cookie')
      .catch((err) =>
        console.log('There has been an error obtaining a cookie', err)
      )
      .then(() => {
        // Login...
        this.getUser();
      });
  },
  methods: {
    getUser() {
      void axios
        .get('api/user')
        .then((response) => {
          this.user = <unknown | null>response.data;
        })
        .catch((err) =>
          console.log('There has been an error with getting User', err)
        );
    },
    handleLogin() {
      void axios
        .post('/login', this.login)
        .catch((err) => console.log('There has been an error with Login', err))
        .then(() => {
          this.getUser();
        });
    },
  },
});
</script>

<style lang="scss">
.form-card-section {
  min-width: 320px;
}
</style>