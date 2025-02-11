<template>
    <Page>
        <ActionBar :title="translations[currentLanguage].login.title" flat="true" class="action-bar">
            <NavigationButton text="Back" android.systemIcon="ic_menu_back" @tap="goBack" />
        </ActionBar>
        <ScrollView>
            <StackLayout class="form-container">
                <StackLayout class="auth-card">
                    <Label :text="translations[currentLanguage].login.title" class="auth-title" />
                    
                    <TextField 
                        v-model="username" 
                        :hint="translations[currentLanguage].login.username" 
                        class="input-field" 
                        keyboardType="text"
                    />
            
                    <TextField 
                        v-model="password" 
                        :hint="translations[currentLanguage].login.password" 
                        :secure="true"
                        class="input-field"
                    />
            
                    <FlexboxLayout class="buttons-container">
                        <Button 
                            :text="translations[currentLanguage].login.loginButton" 
                            @tap="login" 
                            class="action-button"
                        />
                        
                        <Button 
                            :text="translations[currentLanguage].login.registerButton" 
                            @tap="goToRegisterPage" 
                            class="secondary-button"
                        />
                    </FlexboxLayout>
                </StackLayout>
            </StackLayout>
        </ScrollView>
    </Page>
</template>

<script>
import axios from "axios";
import Posts from "./Posts.vue";
import Register from "./Register.vue";
import { goBack } from "@nativescript/core/ui/frame/frame-common";
import { translations } from '../i18n/translations';
import * as applicationSettings from "@nativescript/core/application-settings";

export default {
    data() {
        return {
            username: "",
            password: "",
            translations,
            currentLanguage: 'fr'
        };
    },
    created() {
        // Pega o idioma das configurações do app
        this.currentLanguage = applicationSettings.getString('appLanguage', 'fr');
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
                        applicationSettings.setString('userIdLogged', response.data.userId.toString());
                        applicationSettings.setString('userRole', response.data.role);
                        this.clearFields();
                        this.goToPostsPage();
                    } else {
                        alert(this.translations[this.currentLanguage].login.unexpectedError);
                    }
                } catch (error) {
                    alert(this.translations[this.currentLanguage].login.error);
                    console.error(error);
                }
            } else {
                alert(this.translations[this.currentLanguage].login.fillFields);
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
        },
        goToRegisterPage() {
            this.$navigateTo(Register);
        }
    },
};
</script>

<style scoped>
.action-bar {
    background-color: #ffffff;
    color: #1a1a1a;
}

.form-container {
    padding: 0;
    background-color: #f8f9fa;
}

.auth-card {
    padding: 24;
    margin: 16;
    background-color: white;
    elevation: 2;
    border-radius: 8;
}

.auth-title {
    font-size: 28;
    font-weight: bold;
    color: #1a73e8;
    text-align: center;
    margin-bottom: 24;
}

.input-field {
    margin-bottom: 16;
    padding: 12;
    font-size: 16;
    border-width: 1;
    border-color: #e0e0e0;
    border-radius: 8;
    background-color: #f8f9fa;
}

.buttons-container {
    justify-content: space-between;
    margin-top: 24;
}

.action-button {
    width: 45%;
    background-color: #1a73e8;
    color: white;
    font-size: 16;
    font-weight: bold;
    padding: 12;
    border-radius: 8;
}

.secondary-button {
    width: 45%;
    background-color: #34495e;
    color: white;
    font-size: 16;
    font-weight: bold;
    padding: 12;
    border-radius: 8;
}
</style>
