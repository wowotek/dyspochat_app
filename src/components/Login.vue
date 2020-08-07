<template>
    <div>
        <div class="box has-background-dark">
            <span style="margin-left: 50%;">
                <img src="../assets/logo.png" alt="Logo" width="300" height="" style="transform: translate(-50%, 0); margin-bottom: -1.5%">
            </span>
            <hr style=" margin-bottom: 2%;">
            <div class="field">
                <p class="control has-icons-left">
                    <input v-model="username" class="input" type="text" placeholder="Username">
                    <span class="icon is-small is-left">
                        <i class="fas fa-user"></i>
                    </span>
                </p>
            </div>
            <div class="field">
                <p class="control has-icons-left">
                    <input v-model="password" class="input" type="password" placeholder="Password">
                    <span class="icon is-small is-left">
                        <i class="fas fa-lock"></i>
                    </span>
                </p>
            </div>
            <div class="field has-addons" style="margin-top: 5%">
                <p class="control is-expanded">
                    <button class="button is-fullwidth is-warning" v-on:click="register">Register</button>
                </p>
                <p class="control is-expanded">
                    <button class="button is-fullwidth is-success" v-on:click="login">Login</button>
                </p>
            </div>
        </div>
        <div v-if="message != ''" class="box" v-bind:class="message_class" style="padding: 5px">
            <h3 class="subtitle is-5 has-text-white has-text-centered">{{ message }}</h3>
        </div>
    </div>
</template>

<script>
import Config from '../config';

export default {
    name: "Login",
    data: function() {
        return {
            username: "",
            password: "",
            processing: false,
            message: "",
            message_class: ""
        }
    },
    methods: {
        login: async function(){
            // // Check Fields Validity ---
            // if(!this.username){
                //     this.message = "Username cannot Be Empty";
            //     this.message_class = "has-background-danger";
            //     return;
            // }
            // if(!this.password){
                //     this.message = "Password cannot Be Empty";
            //     this.message_class = "has-background-danger";
            //     return;
            // }
            // --- End Check Fields Validity
            this.processing = true;
            this.axios
                .post(Config.HOST + "/user/login",{
                    username: this.username,
                    password: this.password
                }, {
                    headers: {'x-api-key': 'wowotek-key'}
                })
                .then(response => {
                    this.processing = false;
                    if (response.data.status == "success"){
                        this.$emit("authenticated", false, response.data.session, response.data.user);
                    } else {
                        this.message = "Username or Password is not correct, try again";
                        this.message_class = "has-background-danger";
                    }
                })
                .catch(error => {
                    console.log(error);
                    this.message = "unexpected error, try again";
                    this.message_class = "has-background-danger";
                    this.processing = false;
                });
        },
        register: async function(){
            // Check Fields Validity ---
            // if(!this.username){
                //     this.message = "Username cannot Be Empty";
            //     this.message_class = "has-background-danger";
            //     return;
            // }
            // if(!this.password){
                //     this.message = "Password cannot Be Empty";
            //     this.message_class = "has-background-danger";
            //     return;
            // }
            // --- End Check Fields Validity
            this.processing = true;
            this.axios
                .post(Config.HOST + "/user/register",{
                    username: this.username,
                    password: this.password
                }, {
                    headers: {'x-api-key': 'wowotek-key'}
                })
                .then(response => {
                    this.processing = false;
                    if (response.data.status == "success"){
                        this.message = "You are now registered! try to login";
                        this.message_class = "has-background-success";
                    } else {
                        this.message = "Username Already Exist! Try Again";
                        this.message_class = "has-background-danger";
                    }
                })
                .catch(error => {
                    error;
                    this.message = "unexpected error, try again";
                    this.message_class = "has-background-danger";
                    this.processing = false;
                });
        }
    }
}
</script>