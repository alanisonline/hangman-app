<template>
  <q-page class="row items-center justify-evenly">
    <!-- <example-component
      title="Example component"
      active
    ></example-component> -->
    <div v-if="user">
      <p>User exists</p>
    </div>
    <div v-else>
      <form action="/login" method="post">
        <input type="email" name="email" v-model="login.email" id="email" />
        <input
          type="password"
          name="password"
          v-model="login.password"
          id="password"
        />
        <input type="submit" value="submit" />
      </form>
    </div>
  </q-page>
</template>

<script lang="ts">
// import ExampleComponent from 'components/OptionsComponent.vue';
import Vue from 'vue';
import axios from 'axios';

export default Vue.extend({
  name: 'PageIndex',
  // components: { ExampleComponent },
  data() {
    return {
      user: null,
      login: { email: '', password: '' },
    };
  },
  created() {
    void axios
      .get('/sanctum/csrf-cookie')
      .catch((err) =>
        console.log('There has been an error obtaining a cookie', err)
      )
      .then((response) => {
        // Login...
        console.log(response);
      });
  },
  methods: {
    handleLogin() {
      void axios
        .post('/login', this.login)
        .catch((err) =>
          console.log('There has been an error checking Login', err)
        )
        .then(response => {
          void axios
            .get('api/user')
            .catch((err) =>
              console.log('There has been an error fetching User', err)
            )
            .then(response => {
              // this.user = <unknown> response.data;
              if(!response) console.log('no user response');
              else console.log(response);
            });
        });
    },
  },
});
</script>
