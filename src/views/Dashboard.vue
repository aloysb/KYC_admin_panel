<template>
    <div class="about">
        <p>Connection succesful!</p>
        <h1>This is the dashboard</h1>
        <v-card 
        max-width='1200px'
        class = 'mx-auto pa-12 mt-2'>
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
        <v-text-field
        v-model="search"
        append-icon="mdi-magnify"
        label="Search"
        single-line
        hide-details
      ></v-text-field>
    </v-row>
      <v-data-table :items='this.users' :headers='this.dataHeaders' :search='this.search'>
       <template v-slot:item.rejected_reason="props">
          <v-edit-dialog
            :return-value.sync="props.item.reject_reason"
            large
            persistent
            @save="save(props.item)"
            @cancel="cancel"
          >
            <div>{{ props.item.rejected_reason }}</div>
            <template v-slot:input>
              <div class="mt-4 title">Enter reason for rejection</div>
            </template>
            <template v-slot:input>
              <v-text-field
                v-model="props.item.rejected_reason"
                label="Edit"
                single-line
                counter
                autofocus
              ></v-text-field>
          </template>
        </v-edit-dialog>
            </template>
            <template v-slot:item.actions="{ item }">
                <v-icon small class="mr-2" @click="approveUser(item)">
                    mdi-account-check
                </v-icon>
                <v-icon small @click="rejectUser(item)">
                    mdi-account-cancel
                </v-icon>
            </template>
        </v-data-table>
      </v-card>
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
            search: '',
            dataHeaders: [{
                    text: 'Last Name',
                    value: 'last_name'
                },
                {
                    text: 'First Name',
                    value: 'first_name'
                },
                {
                    text: 'Email',
                    value: 'email'
                }, {
                    text: 'Status',
                    value: 'passed'
                }, {
                    text: 'Reason for rejection',
                    value: 'rejected_reason'
                }, {
                    text: 'Action',
                    value: 'actions'
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
        },
        approveUser(user) {
            const approvedUser = {
                "email": `${user.email}`,
                "status": "approved",
                "rejected_reason": ""
            }

            axios.put('https://kyc.to.wtf/api/admin/kyc_status', approvedUser, this.config);
            this.getAllUsers();
        },
        rejectUser(user) {
            console.log(user.rejected_reason);
            const rejectedUser = {
                "email": `${user.email}`,
                "status": `rejected`,
                "rejected_reason": `${user.rejected_reason}`
            }

            axios.put('https://kyc.to.wtf/api/admin/kyc_status', rejectedUser, this.config);
            console.log(user.first_name + ' ' + user.last_name + ' Has been rejected for ' + user.rejected_reason)
            this.getAllUsers();
        },
        save (user) {
          this.rejectUser(user);
          this.getAllUsers();
          // this.snack = true
          // this.snackColor = 'success'
          // this.snackText = 'Data saved'
        },        
        cancel () {
          // this.snack = true
          // this.snackColor = 'success'
          // this.snackText = 'Data saved'
        },

    },
    created() {
        this.getAllUsers();
    }
}
</script>