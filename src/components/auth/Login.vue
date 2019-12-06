<template>
  <div>
    <div>
      <v-layout justify-center>
        <i class="fab fa-github-square i-github"></i>
      </v-layout>
    </div>
    <v-btn class="btn-login" v-if="!isLoading" outline @click="login">Login with GitHub</v-btn>
    <v-btn class="btn-login" v-if="isLoading" outline>
      <img src="@/assets/loaders/bars.svg" alt="loading...">
    </v-btn>
  </div>
</template>

<script>
import axios from 'axios';
import AuthHelper from '../../config/AuthHelper';
import AxiosHelper from '../../config/AxiosHelper';

export default {
  name: 'Login',
  data() {
    return {
      isLoading: false
    };
  },
  methods: {
    login() {
      window.location = `${AxiosHelper.authUrl}?client_id=${
        AuthHelper.clientId
      }&scope=user%20repo%20read:org`;
    }
  },
  created: function() {
    const code = window.location.href.match(/\?code=(.*)/);
    if (code) {
      this.isLoading = true;
      console.log(this.isLoading);
      axios({
        method: `post`,

        url: `${AxiosHelper.gatekeeperUrl}?client_id=${
          AuthHelper.clientId
        }&client_secret=${
          AuthHelper.clientSecret
        }&code=${code[1].slice(0, 20)}`,

        headers:{
          accept: 'application/json'
        }
      }).then(res => {
          this.isLoading = false;
          localStorage.setItem('token', res.data.token);
          window.location = AxiosHelper.homeUrl;
          console.log(localStorage.getItem('token'));
        })
        .catch(err => {
          this.isLoading = false;
          console.log(err);
        });
    }
  }
};
</script>

<style lang="scss" scoped>
.i-github {
  font-size: 64px;
}

.btn-login {
  width: 170px;
}
</style>