<template>
    <div class="dashboard__wrapper">
        <!-- HEADER LOGOUT-->
        <v-btn v-on:click="logout" class='header__logout' color='error'>Logout</v-btn>
        <v-row>
            <v-col cols='12' class='mx-auto ma-4 d-flex align-center justify-center pa-12'>
                <!-- MAIN CARD -->
                <v-card min-width='100%' max-width='1200px' class='mx-auto pl-12 pr-12 pt-6 elevation-10 align-self-stretch'>
                    <!-- CARD TITLE -->
                    <h1 class='display-1 align-left light-blue darken-2 pa-2 white--text mb-4'>KYC User Datable</h1>
                    <v-row class='d-flex space-between'>
                        <v-col cols='3' class='text-left'>
                            <!-- ADD NEW USER FORM -->
                            <v-dialog persistent v-model='newUserForm' max-width='600px'>
                                <template v-slot:activator="{ on }">
                                    <v-btn color="primary" dark v-on="on" x-large>+ Create new user</v-btn>
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
                        <v-col offset='3' cols='6'>
                            <!-- SEARCH FIELD -->
                            <v-text-field v-model="search" append-icon="mdi-magnify" label="Search user" single-line hide-details></v-text-field>
                        </v-col>
                    </v-row>
                    <!--start of data table--->
                    <v-data-table :items='this.users' :headers='this.dataHeaders' :search='this.search' loading=false loading-text="Loading... Please wait" sortBy="last_name">
                        <!-- DATE FORMATING -->
                        <template v-slot:item.last_update="{ item }">
                            {{formatDate(item.last_update)}}
                        </template>
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
                            <v-chip :color="getColor(item.passed)" dark>{{(item.passed == 0)? 'R':'A'}}</v-chip>
                        </template>
                        <!-- APPROVED OR REJECT BUTTON -->
                        <template v-slot:item.reject="{ item }">
                            <v-icon large class="mr-2" color='green' @click="approveUser(item)">
                                mdi-account-check
                            </v-icon>
                        </template>
                        <template v-slot:item.approve="{ item }">
                            <v-icon large color='red' @click="rejectUser(item)">
                                mdi-account-cancel
                            </v-icon>
                        </template>
                    </v-data-table>
                </v-card>
            </v-col>
        </v-row>
        <v-snackbar v-model="snack" :timeout="3000" :color="snackColor" top>
            {{ snackText }}
            <v-btn text @click="snack = false">Close</v-btn>
        </v-snackbar>
    </div>
</template>
<script>
import axios from 'axios';
import moment from 'moment';

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
            snackColor: 'green',
            snackText: 'Hello world',
            loading: true,
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
                    text: 'Updated',
                    value: 'last_update'
                }, {
                    text: 'Status',
                    value: 'passed',
                }, {
                    text: 'Reason for rejection',
                    value: 'rejected_reason'
                }, {
                    text: '',
                    value: 'approve'
                }, {
                    text: '',
                    value: 'reject'
                },
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
                .then(this.loading = false)
                .catch(err => console.log(err));
        },
        createNewUser() {
          //BASIC DATA VALIDATION => Does not check for the type of data and does not do any sanitazition
            if (this.newUser.email.length == 0){
                this.snack = true
                this.snackColor = 'error'
                this.snackText = `Please enter email.`
            } else if (this.newUser.last_name.length == 0 || this.newUser.first_name == 0){
                  this.snack = true
                this.snackColor = 'error'
                this.snackText = `Please enter first name and last name`
            } else {
                axios.post('https://kyc.to.wtf/api/admin/new_user', this.newUser, this.config)
                    .then(res => {
                        // Error handling needs to display something to the user
                        if (res.status != 200) throw Error;
                        //Close the form
                        this.newUserForm = false;
                        //Fetch the users again.
                        this.getAllUsers()
                    })
                    .then(() => {
                        this.snack = true;
                        this.snackColor = 'success';
                        this.snackText = 'User created!';
                      })
                    .catch(err => console.log(err));
            }
        },
        approveUser(user) {
            const approvedUser = {
                "email": `${user.email}`,
                "status": "approved",
                "rejected_reason": ""
            }


            axios.put('https://kyc.to.wtf/api/admin/kyc_status', approvedUser, this.config)
                .then(() => {
                    this.snack = true
                    this.snackColor = 'success'
                    this.snackText = `${user.first_name} ${user.last_name} approved!`
                })
                .then(() => this.getAllUsers())
                .catch(err => console.log(err))
        },
        rejectUser(user) {
            console.log(user.rejected_reason);
            const rejectedUser = {
                "email": `${user.email}`,
                "status": `rejected`,
                "rejected_reason": `${user.rejected_reason}`
            }

            axios.put('https://kyc.to.wtf/api/admin/kyc_status', rejectedUser, this.config).
            then(() => {
                    this.snack = true
                    this.snackColor = 'error'
                    this.snackText = `${user.first_name} ${user.last_name} rejected!`
                }).then(() => this.getAllUsers())
                .catch(err => console.log(err))
        },
        formatDate(date) {
            return moment(date).fromNow()
        },

        save(user) {
            this.rejectUser(user).
            then(() => {
                    this.snack = true
                    this.snackColor = 'success'
                    this.snackText = 'Data saved'
                }).then(() => this.getAllUsers())
                .catch(err => console.log(err))

        },
        cancel() {
            this.snack = true;
            this.snackColor = 'warning';
            this.snackText = 'Data not saved.'
        },

        getColor(status) {
            return (status != 0) ? "green" : "red"
        },

        logout() {
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
.dashboard__wrapper {
    min-height: 100vh;
    width: 100vw;
    background: #136a8a;
    /* fallback for old browsers */
    background: -webkit-linear-gradient(to right, #267871, #136a8a);
    /* Chrome 10-25, Safari 5.1-6 */
    background: linear-gradient(to right, #267871, #136a8a);
    /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */

}

/*  .header{
    background: rgba(0, 172, 193,0.3)
  }*/

.header__logout {
    position: absolute;
    right: 0;
    top: 0;
    margin: 1em;
}

/*Data table*/
table {
    margin-top: 1em;
    border-radius: 10px;
    border: .5px solid #aaa;
}

tr {
    border-radius: 10px;
}

.v-data-table-header th {
    align-content: center;
    justify-content: center;
    font-size: .8rem;
    background: #E3F2FD;
    padding: 0;
    border-top: .5px solid #aaa;
    border-bottom: 1px solid #aaa;
}

.v-application--is-ltr .v-data-table th {
    text-align: center !important;
}

.v-data-table-header th:first-child {
    border-radius: 10px 0 0 0;
    border-left: .5px solid #aaa;

}

.v-data-table-header th:last-child {
    border-radius: 0 10px 0 0;
    border-right: .5px solid #aaa;

}
</style>