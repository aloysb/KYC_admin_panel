<template>
    <div>
      <header class = 'header pt-3 pb-3 mb-9 blue darken-3'>
        <h1 class = 'display-1'>Welcome to the dashboard</h1>
        <v-btn v-on:click="logout" class = 'header__logout' color='error'>Logout</v-btn>
      </header> 
        <v-card max-width='1200px' class='mx-auto pa-12 mt-2'>
            <v-row justify='space-between'>
              <v-col cols = '3'>

                <!-- ADD NEW USER FORM -->
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
              </v-col>
              <v-col cols= '6'>

                <!-- SEARCH FIELD -->
                <v-text-field v-model="search" append-icon="mdi-magnify" label="Search user" single-line hide-details></v-text-field>
              </v-col>
            </v-row>
            <v-data-table :items='this.users' :headers='this.dataHeaders' :search='this.search'>

              <!-- EDITABLE REJECTED REASON -->
                <template v-slot:item.rejected_reason="props">
                    <v-edit-dialog :return-value.sync="props.item.reject_reason" large persistent @save="save(props.item)" @cancel="cancel">
                        <div>{{ props.item.rejected_reason }}</div>
                        <template v-slot:input>
                            <div class="mt-4 title">Enter reason for rejection</div>
                        </template>
                        <template v-slot:input>
                            <v-text-field v-model="props.item.rejected_reason" label="Edit" single-line counter autofocus></v-text-field>
                        </template>
                    </v-edit-dialog>
                </template>

                <!-- STATUS COLOR -->
                    <template v-slot:item.passed="{ item }">
      <v-chip :color="getColor(item.passed)" dark></v-chip>
    </template>

              <!-- APPROVED OR REJECT BUTTON -->
                <template v-slot:item.actions="{ item }">
                    <v-icon small class="mr-2" @click="approveUser(item)" :large='true'>
                        mdi-account-check
                    </v-icon>
                    <v-icon small @click="rejectUser(item)">
                        mdi-account-cancel
                    </v-icon>
                </template>
            </v-data-table>
        </v-card>
 <!--        <v-snackbar v-model="snack" :timeout="3000" :color="snackColor">
      {{ snackText }}
      <v-btn text @click="snack = false">Close</v-btn>
    </v-snackbar> -->
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
            snack: false,

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
            axios.post('https://kyc.to.wtf/api/admin/new_user', this.newUser, this.config)
                .then(res => {
                    // Error handling needs to display something to the user
                    if (res.status != 200) throw Error;
                    console.log('New user succesfully created')
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
            this.getAllUsers();
        },
        save(user) {
            this.rejectUser(user);
            this.getAllUsers();
            // this.snack = true
            // this.snackColor = 'success'
            // this.snackText = 'Data saved'
        },
        cancel() {
          //Nothing
        },

        getColor(status){
          return (status != 0)? "green":"red"
        },

        logout(){
          this.$emit('logged-out');
          this.$router.push('/');
        }

    },
    created() {
        this.getAllUsers();
    }
}
</script>
<style sccoped>
  .header{
    color: #eee;
  }

  .header__logout{
    position: absolute;
    right: 0;
    top: 0;
    margin: 1em;
    font-weight: 700;
  }

  /*Data table*/
  table{
    margin-top: 1em;
    border-radius: 10px;
    border: .5px solid #aaa;
  }

  tr{
        border-radius: 10px;
  }
  .v-data-table-header th{
    align-content: center;
    justify-content: center;
    font-size: 1rem;
    background: #E3F2FD;
    border-top: .5px solid #aaa;
    border-bottom: 1px solid #aaa;
  }

   .v-data-table-header th:first-child{
            border-radius: 10px 0 0 0;
        border-left: .5px solid #aaa;

   }

      .v-data-table-header th:last-child{
        border-radius: 0 10px 0 0;
        border-right: .5px solid #aaa;

   }
</style>