<template>
  <v-app>
  <div id="app">
    <router-view v-on:logged-in='loggedIn' v-on:logged-out='loggedOut' v-bind:token="this.token" />
  </div>
</v-app>
</template>

<script>
  export default{
  name: 'App',
  data(){
    return{
      token:'',
      headers:''
    }
  },
  methods:{
    loggedIn(token){
      //Save token;
      this.token = token;
    },
    loggedOut(){
      //Remove token
      this.token = '';
    }   
  }
}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav {
  padding: 30px;

  a {
    font-weight: bold;
    color: #2c3e50;

    &.router-link-exact-active {
      color: #42b983;
    }
  }

}
</style>
