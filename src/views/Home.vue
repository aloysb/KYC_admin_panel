<template>
  <div>
  <Login v-on:logged-in='loggedIn'/>
</div>
</template>

<script>
// @ is an alias to /src
import Login from '@/components/Login';

export default {
  name: 'Home',
  components: {
    Login
  },
  methods:{
    loggedIn(token){
      //Pass the token up to the App for shared State.
      this.$emit('logged-in', token)
    }
  }
}
</script>
