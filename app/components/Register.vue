<template>
    <Page>
      <ActionBar title="Register" />
      <StackLayout class="form-container">
        <TextField 
          v-model="username" 
          hint="Enter your username" 
          class="input" 
          keyboardType="text"
        />
  
        <TextField 
          v-model="password" 
          hint="Enter your password" 
          secureTextEntry="true" 
          class="input"
        />
  
        <TextField 
          v-model="role" 
          hint="Enter your role" 
          class="input"
        />
  
        <Button 
          text="Register" 
          @tap="registerUser" 
          class="button"
        />

        <Button 
          text="Login" 
          @tap="goToLoginPage" 
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
  import Login from "./Login.vue"; 

  export default {
    data() {
      return {
        username: "",
        password: "",
        role: "",
        error: null
      };
    },
    methods: {
      goBack() {
        this.$navigateBack();
      },
      async registerUser() {
        if (this.username && this.password && this.role) {
          try {
            const response = await axios.post("http://10.0.2.2:3000/register", {
              username: this.username,
              password: this.password,
              role: this.role,
            });

            if (response && response.status === 201) {
              alert(response.data.message || "User registered successfully!");
              this.clearFields();
              this.$navigateTo(Login);
            } else {
              alert(`Unexpected response: ${response ? response.status : "null"}`);
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
        this.role = "";
      },
      goToLoginPage() {
        this.$navigateTo(Login); 
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
