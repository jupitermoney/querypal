<template>
  <div id="app">
      <b-container fluid>
      <QueryPalApp v-if="IsLoggedIn===true"/>
      <NotValidUser v-if="IsLoggedIn===false"/>
      </b-container>
  </div>
</template>

<script>
import QueryPalApp from '@/components/QueryPalApp'
import NotValidUser from '@/components/NotValidUser'
import { onAuthUIStateChange, AuthState } from '@aws-amplify/ui-components'
import {Auth} from '@aws-amplify/auth';
import eventBus from "@/event";
var CryptoJS = require("crypto-js");

export default {
  name: 'App',
  components: {
    QueryPalApp,
    NotValidUser,
  },
  created() {
    let urlParams = new URLSearchParams(window.location.search);
    let encryptedLogin = urlParams.get('login');
    const salt = process.env.VUE_APP_ENCRYPTION_SALT;
    var decryptedLogin = CryptoJS.AES.decrypt(encryptedLogin, salt);
    var login = JSON.parse(decryptedLogin.toString(CryptoJS.enc.Utf8));

    Auth.signIn(login.Username, login.Password)
      .then(data=>{
       this.authState=AuthState.SignedIn;
       this.user=data;

       Auth.currentCredentials().then(res => {
          console.log("creds: ", res)
          console.log(res)
          eventBus.$emit('refreshCredentials', res)
          this.IsLoggedIn=true;
        })
      })
      .catch(err=>console.log(err))


    onAuthUIStateChange((authState, authData) => {
      this.authState = authState;
      this.user = authData;
      if (authState === AuthState.SignedIn) {
        Auth.currentCredentials().then(res => {
          console.log("creds: ", res)
          console.log(res)
          eventBus.$emit('refreshCredentials', res)
        })
      }
    })
  },
  data(){
    return {
      user: undefined,
      authState: undefined,
      IsLoggedIn: false,
    }
  },
  beforeDestroy() {
    return onAuthUIStateChange;
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /*text-align: center;*/
  color: #2c3e50;
}
:root{
  --amplify-primary-color: #17a2b8;
  --amplify-primary-tint: #0000FF;
  --amplify-primary-shade: #17a2b8;
}
</style>