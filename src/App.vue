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
        let myParam = urlParams.get('login');

        let promise = new Promise((resolve,reject)=>{
          let abcd = CryptoJS.AES.decrypt(myParam, 'secret key 123');
           console.log(abcd);
           resolve(abcd);
           reject(console.log("some thing went wrong"));
        })

        promise.then(bytes=>{
            let promise1 = new Promise((resolve,reject)=>{
                resolve({decryptedData : JSON.parse(bytes.toString(CryptoJS.enc.Utf8))})
                reject(console.log("some thing went wrong"));
            })

            promise1.then(decryptedData => {
                  console.log(decryptedData[0].Username);
                  console.log(decryptedData[1].Password);
            })
        });

    Auth.signIn("harjindersingh.mistry@jupiter.money","Welcome@123")
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