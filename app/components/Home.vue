<template>
    <Page>
        <ActionBar :title="currentLanguage === 'en' ? 'Blog Minimalist' : 'Blog Minimaliste'" flat="true" class="action-bar">
            <FlexboxLayout horizontalAlignment="right" class="language-toggle">
                <Label text="FR" :class="{ 'active': currentLanguage === 'fr' }" @tap="setLanguage('fr')" />
                <Label text="/" class="separator" />
                <Label text="EN" :class="{ 'active': currentLanguage === 'en' }" @tap="setLanguage('en')" />
            </FlexboxLayout>
        </ActionBar>
        <ScrollView>
            <StackLayout class="home-container">
                <StackLayout class="welcome-card">
                    <Label :text="translations[currentLanguage].welcome" class="welcome-title" />
                    <TextView 
                        :text="translations[currentLanguage].description" 
                        class="welcome-content" 
                        editable="false" 
                    />
                    <Button :text="translations[currentLanguage].getStarted" @tap="goToNextPage" class="action-button" />
                </StackLayout>
            </StackLayout>
        </ScrollView>
    </Page>
</template>

<script>
import Register from "./Register.vue";
import { translations } from '../i18n/translations';
import * as applicationSettings from "@nativescript/core/application-settings";

export default {
    data() {
        return {
            translations,
            currentLanguage: 'fr'
        };
    },
    created() {
        this.currentLanguage = applicationSettings.getString('appLanguage', 'fr');
    },
    methods: {
        goToNextPage() {
            this.$navigateTo(Register);
        },
        setLanguage(lang) {
            this.currentLanguage = lang;
            applicationSettings.setString('appLanguage', lang);
        }
    },
};
</script>

<style scoped>
.action-bar {
    background-color: #ffffff;
    color: #1a1a1a;
}

.home-container {
    padding: 0;
    background-color: #f8f9fa;
}

.welcome-card {
    padding: 24;
    margin: 16;
    background-color: white;
    elevation: 2;
    border-radius: 8;
}

.welcome-title {
    font-size: 28;
    font-weight: bold;
    color: #1a73e8;
    text-align: center;
    margin-bottom: 20;
}

.welcome-content {
    font-size: 16;
    color: #333333;
    line-height: 1.5;
    text-align: justify;
    margin: 16 0;
    background-color: transparent;
    border-width: 0;
}

.action-button {
    margin-top: 24;
    background-color: #1a73e8;
    color: white;
    font-size: 18;
    font-weight: bold;
    padding: 16;
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

.language-selector, .language-button, .language-active {
    display: none;
}
</style>
