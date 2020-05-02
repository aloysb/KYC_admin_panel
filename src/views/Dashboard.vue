<template>
  <div class="about">
    <p>Connection succesful!</p>
    <h1>This is the dashboard</h1>
    <v-btn v-on:click="createNewUser()"></v-btn>
  </div>
</template>


<script>
  import axios from 'axios';

  export default{
    name: 'Dashboard',
    data(){
      return{
        //Config for API request.
        config: {headers: {
            'Content-Type': 'application/json',
            'Authorization': `Token ${this.token}`
          }
        },
        users:[]
      }
    },
    props:['token'],
    methods:{
      getAllUsers(){
        axios.get('https://kyc.to.wtf/api/admin/users',this.config)
        .then(res => {
          if (res.status != 200) throw Error;
          this.users = res.data.items;
        })
        .catch(err => console.log(err));
      },
      createNewUser(){

      }
    },
    created(){
      this.getAllUsers();
    }
  }
</script>