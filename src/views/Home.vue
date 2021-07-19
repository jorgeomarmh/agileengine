<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue';
import axios from "axios";

export default {
  name: 'Home',
  components: {
    HelloWorld
  },
  data(){
    return{
      config: {
        method: 'post',
        url: 'http://interview.agileengine.com/auth',
        headers: { 
          'Content-Type': 'text/plain'
        },
        data : { apiKey: "23567b218376f79d9415" }
        }
    }
  },
  onload(){
    this.auth()
  },
  methods:{
    auth(){
      axios(this.config).then(function (response) {
        console.log(JSON.stringify(response.data));

      }).catch(function (error) {
        console.log(error);
      });
    }
  }
}
</script>
