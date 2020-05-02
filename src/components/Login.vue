<template>
    <v-col cols='6' class = 'mx-auto'>
        <v-form class = 'elevation-7 pa-12 form'>
            <h1>Hello,</h1>
            <h2>Please login to access the admin panel</h2>
            <v-text-field v-model='email' label='Email' required clearable>
            </v-text-field>
            <v-text-field v-model='password' label='Password' :append-icon="show1 ? 'mdi-eye' : 'mdi-eye-off'" :type="show1 ? 'text' : 'password'" required @click:append="show1 = !show1">
            </v-text-field>
            <v-alert type ='error' v-if="badCredentials==true"> Wrong credentials. Please check your login details</v-alert>
            <div class = 'text-center mt-5'>
            <v-btn @click='submit'>
                Login
            </v-btn>
          </div>
        </v-form>
    </v-col>
</template>
<script>
import axios from 'axios';

export default {
    name: 'Login',

    data() {
        return {
            show1: false,
            email: '',
            password: '',
            badCredentials: false,
            data:'',
        }
    },

    methods: {
        submit() {
            this.badCredentials = false;

            axios.post('https://kyc.to.wtf/api/admin/login',{ "email": this.email,
              "password": this.password})
                //Check status code. If OK, save in state.
              .then(res => {
                if(res.data.code != 200) throw Error;
                this.saveJWT(res.data.token)
              })
              //redirect to dashboard
                .then(() => {this.$router.push('dashboard')})
                //WRONG - CREDENTIALS
              .catch(err => {
                console.log(err);
                this.badCredentials = true});
        },
        saveJWT(token){
            this.$emit('logged-in', token);
        }
    }
}

</script>

<style scoped>
  .form{
    border-radius: 10px;
  }
</style>