<template>
    <Page>
        <ActionBar :title="translations[currentLanguage].profile.title" flat="true" class="action-bar">
            <NavigationButton text="Back" android.systemIcon="ic_menu_back" @tap="goBack" />
            <FlexboxLayout horizontalAlignment="right" class="language-toggle">
                <Label text="FR" :class="{ 'active': currentLanguage === 'fr' }" @tap="setLanguage('fr')" />
                <Label text="/" class="separator" />
                <Label text="EN" :class="{ 'active': currentLanguage === 'en' }" @tap="setLanguage('en')" />
            </FlexboxLayout>
        </ActionBar>
        <ScrollView>
            <StackLayout class="profile-container">
                <StackLayout class="profile-card">
                    <FlexboxLayout class="post-header">
                                <Label text="@" class="user-icon" />
                                <Label :text="'user_' + post.user_id" class="post-user" />
                            </FlexboxLayout>
                    <TextField 
                        v-model="username" 
                        :hint="translations[currentLanguage].profile.username" 
                        class="input-field" 
                    />
                    <TextField 
                        v-model="password" 
                        :hint="translations[currentLanguage].profile.password" 
                        :secure="true"
                        class="input-field"
                    />
                    
                    <FlexboxLayout class="buttons-container">
                        <Button 
                            :text="translations[currentLanguage].profile.updateButton" 
                            @tap="updateProfile" 
                            class="action-button"
                        />
                        <Button 
                            :text="translations[currentLanguage].profile.logout" 
                            @tap="logout" 
                            class="delete-button"
                        />
                    </FlexboxLayout>
                </StackLayout>
            </StackLayout>
        </ScrollView>
    </Page>
</template>

<script>
import axios from "axios";
import { translations } from '../i18n/translations';
import * as applicationSettings from "@nativescript/core/application-settings";
import Home from "./Home.vue";

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
        this.currentLanguage = applicationSettings.getString('appLanguage', 'fr');
        this.username = applicationSettings.getString('usernameLogged');
    },
    methods: {
        async updateProfile() {
            try {
                const response = await axios.put("http://10.0.2.2:3000/user", {
                    newUsername: this.username,
                    newPassword: this.password
                }, {
                    headers: {
                        Authorization: `Bearer ${applicationSettings.getString("authToken")}`,
                    },
                });

                if (response.status === 200) {
                    applicationSettings.setString('usernameLogged', this.username);
                    this.password = ""; 
                    alert(this.translations[this.currentLanguage].profile.updateSuccess);
                }
            } catch (error) {
                alert(this.translations[this.currentLanguage].profile.updateError);
            }
        },
        logout() {
            applicationSettings.remove('authToken');
            applicationSettings.remove('usernameLogged');
            applicationSettings.remove('userIdLogged');
            applicationSettings.remove('userRole');
            this.$navigateTo(Home, { clearHistory: true });
        },
        goBack() {
            this.$navigateBack();
        },
        setLanguage(lang) {
            this.currentLanguage = lang;
            applicationSettings.setString('appLanguage', lang);
            this.$forceUpdate();
        }
    }
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

.language-toggle {
    margin-right: 16;
    align-items: center;
}

.language-toggle Label {
    font-size: 16;
    font-weight: bold;
    padding: 4 8;
    color: #666666;
}

.language-toggle .separator {
    color: #666666;
    margin: 0 4;
}

.language-toggle .active {
    color: #1a73e8;
}

.section-title,
.language-selector,
.language-button,
.language-button.active {
    display: none;
}

.user-icon {
    width: 32;
    height: 32;
    padding: 6;
    margin-right: 8;
    text-align: center;
    color: #ffffff;
    background-color: #1a73e8;
    border-radius: 16;
    font-size: 14;
}

.post-user {
    font-size: 16;
    font-weight: 500;
    color: #1a1a1a;
    padding-top: 6;
}
</style>