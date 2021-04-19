<template>
  <q-page class="row items-center justify-evenly">
    <!-- <example-component
      title="Example component"
      active
    ></example-component> -->
    <div v-if="user">
      <p>{{ this.user.hasOwnProperty('name') ? this.user.name : '' }}</p>
      <GameComponent/>
    </div>
    <div v-else>
      <q-card square bordered class="q-pa-lg shadow-1">
        <q-card-section>
          <q-form class="q-gutter-md" method="POST" @submit="handleLogin" action="/login">
            <q-input square v-model="login.email" type="email" label="email" />
            <q-input
              square
              filled
              clearable
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
        <q-card-actions class="q-px-md">
        </q-card-actions>
        <q-card-section class="text-center q-pa-none">
          <p class="text-grey-6">
            Not registered?
            <q-btn flat class="text-blue-3" v-on:click="$emit('register')"
              >Create an Account</q-btn
            >
          </p>
        </q-card-section>
      </q-card>
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
          console.log(response.data);
          this.user = <unknown | null>response.data;
          console.log(this.user);
        })
        .catch((err) => console.log('There has been an error with getting User', err));
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
