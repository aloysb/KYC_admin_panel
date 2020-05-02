<template>
    <div class="about">
        <p>Connection succesful!</p>
        <h1>This is the dashboard</h1>
        <v-row justify='center'>
            <v-dialog persistent v-model='newUserForm' max-width='600px'>
                <template v-slot:activator="{ on }">
                    <v-btn color="primary" dark v-on="on">+ Create new user</v-btn>
                </template>
                <v-card>
                    <v-form class='elevation-7 px-12 py-6'>
                        <v-text-field v-model='newUser.first_name' placeholder='First name'></v-text-field>
                        <v-text-field v-model='newUser.middle_name' placeholder='Middle name'></v-text-field>
                        <v-text-field v-model='newUser.last_name' placeholder='Last name'></v-text-field>
                        <v-text-field v-model='newUser.email' placeholder='Email'></v-text-field>
                        <v-card-actions>
                            <v-spacer></v-spacer>
                            <v-btn color="error darken-1" text @click="newUserForm = false">Close</v-btn>
                            <v-btn color="primary darken-1" text @click="createNewUser">Save</v-btn>
                        </v-card-actions>
                    </v-form>
                </v-card>
            </v-dialog>
        </v-row>
        <v-data-table :items='this.users' :headers='this.headers'>
        </v-data-table>
    </div>
</template>
<script>
import axios from 'axios';

export default {
    name: 'Dashboard',
    data() {
        return {
            //Config for API request.
            config: {
                headers: {
                    'Content-Type': 'application/json',
                    'Authorization': `Token ${this.token}`
                }
            },
            users: [],
            newUser: {
                "email": "",
                "first_name": "",
                "middle_name": "",
                "last_name": ""
            },
            newUserForm: false,
            headers: [
              {
                text: 'First Name',
                value: 'first_name'
              },
              {
                text: 'Last Name',
                value: 'last_name'
              },

              {
                text: 'Email',
                value: 'email'
              },{
                text: 'Status',
                passed: 'passed'
              }
            ]
        }
    },
    props: ['token'],
    methods: {
        getAllUsers() {
            axios.get('https://kyc.to.wtf/api/admin/users', this.config)
                .then(res => {
                    if (res.status != 200) throw Error;
                    this.users = res.data.items;
                })
                .catch(err => console.log(err));
        },
        createNewUser() {
            // !!! this.newUser needs to be filled via a form, this is for development purpose only/
            axios.post('https://kyc.to.wtf/api/admin/new_user', this.newUser, this.config)
                .then(res => {
                    // Error handling needs to display something to the user
                    if (res.status != 200) throw Error;
                    console.log('New users succesfully created')
                    //Close the form
                    this.newUserForm = false;
                    //Fetch the users again.
                    this.getAllUsers()
                })
        }
    },
    created() {
        this.getAllUsers();
    }
}
</script>