<template>
    <div class = 'login__wrapper d-flex justify-center align-center'>
    <v-col lg='6' sm='8' xs='10'  class = 'mx-auto'>
        <v-form class = 'elevation-7 pa-12 form white'>
            <h1 class='text-left display-3 pb-2'>Hello !</h1>
            <h2 class='text-left mb-3 pl-1 grey--text text--darken-2 font-weight-thin'>Please login to access the admin panel</h2>
            <v-text-field v-model='email' label='Email' required clearable>
            </v-text-field>
            <v-text-field v-model='password' label='Password' :append-icon="show1 ? 'mdi-eye' : 'mdi-eye-off'" :type="show1 ? 'text' : 'password'" required @click:append="show1 = !show1">
            </v-text-field>
            <v-alert type ='error' v-if="badCredentials==true"> Wrong credentials. Please check your login details</v-alert>
            <div class = 'mt-5'>
            <v-btn x-large @click='submit' color='primary'>
                Login
            </v-btn>
          </div>
        </v-form>
    </v-col>
</div>
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
.login__wrapper{
    height: 100vh;
    width: 100vw;
    background: #136a8a;  /* fallback for old browsers */
background: -webkit-linear-gradient(to right, #267871, #136a8a);  /* Chrome 10-25, Safari 5.1-6 */
background: linear-gradient(to right, #267871, #136a8a); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */


}
  .form{
    border-radius: 10px;
  }

</style>