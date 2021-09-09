<template>
  <div id="app">
      <b-container fluid>
        <QueryPalApp/>
      </b-container>
  </div>
</template>

<script>
import QueryPalApp from '@/components/QueryPalApp'
import { onAuthUIStateChange, AuthState } from '@aws-amplify/ui-components'
import {Auth} from '@aws-amplify/auth';
import eventBus from "@/event";
var CryptoJS = require("crypto-js");

export default {
  name: 'App',
  components: {
    QueryPalApp,
  },
  created() {
    let urlParams = new URLSearchParams(window.location.search);
    let encryptedLogin = urlParams.get('login');

    console.log("encrypted login: " + encryptedLogin);

    cosnt salt = baf5pm)Ph)!)9v#;
    const salt1 = baf5pm)Ph)!)9v#;

    console.log(process.env.VUE_APP_ENCRYPTION_SALT);

    console.log("salt1 "+ salt1);
    console.log("salt: " + salt);
    var bytes = CryptoJS.AES.decrypt(encryptedLogin, salt);
    var login = JSON.parse(bytes.toString(CryptoJS.enc.Utf8));
    console.log(login[0].Username);
    console.log(login[1].Password);

    Auth.signIn(login[0].Username, login[1].Password)
      .then(data=>{
       this.authState=AuthState.SignedIn;
       this.user=data;

       Auth.currentCredentials().then(res => {
          console.log("creds: ", res)
          console.log(res)
          eventBus.$emit('refreshCredentials', res)
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
      authState: undefined
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