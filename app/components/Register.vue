<template>
    <Page>
        <ActionBar :title="translations[currentLanguage].register.title" flat="true" class="action-bar">
            <NavigationButton text="Back" android.systemIcon="ic_menu_back" @tap="goBack" />
        </ActionBar>
        <ScrollView>
            <StackLayout class="form-container">
                <StackLayout class="auth-card">
                    <Label :text="translations[currentLanguage].register.title" class="auth-title" />
                    
                    <TextField 
                        v-model="username" 
                        :hint="translations[currentLanguage].register.username" 
                        class="input-field" 
                        keyboardType="text"
                    />
            
                    <TextField 
                        v-model="password" 
                        :hint="translations[currentLanguage].register.password" 
                        :secure="true"
                        class="input-field"
                    />
            
                    <TextField 
                        v-model="role" 
                        :hint="translations[currentLanguage].register.role" 
                        class="input-field"
                    />
            
                    <FlexboxLayout class="buttons-container">
                        <Button 
                            :text="translations[currentLanguage].register.registerButton" 
                            @tap="registerUser" 
                            class="action-button"
                        />
                        
                        <Button 
                            :text="translations[currentLanguage].register.loginButton" 
                            @tap="goToLoginPage" 
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
import Login from "./Login.vue";
import { translations } from '../i18n/translations';
import * as applicationSettings from "@nativescript/core/application-settings";

export default {
    data() {
        return {
            username: "",
            password: "",
            role: "",
            error: null,
            translations,
            currentLanguage: 'fr'
        };
    },
    created() {
        this.currentLanguage = applicationSettings.getString('appLanguage', 'fr');
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
                        alert(this.translations[this.currentLanguage].register.success);
                        this.clearFields();
                        this.$navigateTo(Login);
                    }
                } catch (error) {
                    alert(this.translations[this.currentLanguage].register.error);
                    console.error(error);
                }
            } else {
                alert(this.translations[this.currentLanguage].register.fillFields);
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

.language-toggle, 
.language-toggle Label, 
.language-toggle .separator, 
.language-toggle .active {
    display: none;
}
</style>
