<template>
  <div class="about">
    <p>Connection succesful!</p>
    <h1>This is the dashboard</h1>
    <v-btn v-on:click="createNewUser()">Create a new user</v-btn>
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
        users:[],
        //DEVELOPMENT PURPOSE ONLY
        newUser: {
          "email": "newuser2@test.com",
          "first_name": "alo",
          "middle_name": "",
          "last_name": "test"
        },
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
        // !!! this.newUser needs to be filled via a form, this is for development purpose only/
        axios.post('https://kyc.to.wtf/api/admin/new_user', this.newUser, this.config)
        .then(res => {
          // Error handling needs to display something to the user
          if(res.status != 200) throw Error;
          console.log('New users succesfully created')
          //Fetch the users again.
          this.getAllUsers()
        })
      }
    },
    created(){
      this.getAllUsers();
    }
  }
</script>