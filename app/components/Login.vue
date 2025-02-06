<template>
    <Page>
      <ActionBar title="Login" />
      <StackLayout class="form-container">
        <TextField 
          v-model="username" 
          hint="Enter your username" 
          class="input" 
          keyboardType="email"
        />
  
        <TextField 
          v-model="password" 
          hint="Enter your password" 
          secure="true" 
          class="input"
        />
  
        <Button 
          text="Login" 
          @tap="login" 
          class="button"
        />

        <Button 
          text="Go To Posts" 
          @tap="goToPostsPage" 
          class="button"
        />

        <Button 
          text="Go Back" 
          @tap="goBack" 
          class="button-back"
        />

      </StackLayout>
    </Page>
  </template>
  
  <script>
  import axios from "axios";
  import Posts from "./Posts.vue";
  import { goBack } from "@nativescript/core/ui/frame/frame-common";
  import * as applicationSettings from "@nativescript/core/application-settings";
  
  export default {
    data() {
      return {
        username: "",
        password: "",
      };
    },
    methods: {
      goBack() {
        this.$navigateBack();
      },
      async login() {
        if (this.username && this.password) {
            try {
                const response = await axios.post("http://10.0.2.2:3000/login", {
                    username: this.username,
                    password: this.password,
                });
                if (response && response.status === 201) {
                    applicationSettings.setString('authToken', response.data.token);
                    applicationSettings.setString('usernameLogged', this.username);
                    this.clearFields();
                    this.goToPostsPage();
                } else {
                    alert(`Unexpected response: ${response ? response.status : "something went wrong"}`);
                }
            } catch (error) {
                if (error.response) {
                    alert(`Error: ${error.response.data.message}`);
                } else {
                    alert("An error occurred. Please try again.");
                }
                console.error(error);
                }
        } else {
                    alert("Please fill in all fields.");
                }
    },
      clearFields() {
        this.username = "";
        this.password = "";
      },
      goToLoginPage() {
        this.$navigateTo(Login); 
      },
      goToPostsPage() {
        this.$navigateTo(Posts); 
      }
    },
  };
  </script>
  
  <style scoped>
  .form-container {
    padding: 20;
    margin: 20;
  }
  
  .input {
    margin-bottom: 15;
    padding: 10;
    font-size: 18;
    border-width: 1;
    border-color: #ccc;
    border-radius: 5;
  }
  
  .button {
    margin-top: 10;
    padding: 10;
    font-size: 18;
    background-color: #4CAF50;
    color: white;
    text-align: center;
    border-radius: 5;
  }

  .button-back {
    margin-top: 10;
    padding: 10;
    font-size: 18;
    background-color: #f44336;
    color: white;
    text-align: center;
    border-radius: 5;
}
  </style>
