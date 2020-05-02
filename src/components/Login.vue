<template>
    <v-col cols='6'>
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
        }
    },

    methods: {
        submit() {
            this.badCredentials = false;
            //Add to use cors-anywhere to allow for CORS. We might have to fix that.
            axios.post('https://kyc.to.wtf/api/admin/login',{ "email": this.email,
              "password": this.password})
              .then(res => console.log('TOKEN ' + res.data.token))
              .catch(() => this.badCredentials = true)
            // console.log(this.email)
            // if (this.email == "email" & this.password == "admin123"){
            //   this.badCredentials = false;
            //   this.$emit('logged-in');
            // } else {
            //   this.badCredentials = true;
            
        }
    }
}

</script>

<style scoped>
  .form{
    border-radius: 10px;
  }
</style>