<template>
  <div class="about">
    <p>Connection succesful!</p>
    <h1>This is the dashboard</h1>
    <button v-on:click="getAllUsers(token)">Get all users</button>
  </div>
</template>


<script>
  export default{
    name: 'Dashboard',
    data(){
      return{
      }
    },
    props:['token'],
    methods:{
      getAllUsers(headers){
        console.log('here');
        console.log(headers);
      }
    }
  }
</script>