<template>
    <Page>
        <ActionBar title="Blog Minimaliste" flat="true" class="action-bar" />
        <ScrollView>
            <StackLayout class="home-container">
                <StackLayout class="welcome-card">
                    <Label text="Bienvenue au blog!" class="welcome-title" />
                    <TextView 
                        text="Ce blog est un exemple d'application mobile en utilisant NativeScript-Vue intégré à une API qui a été faite dans NodeJS. Ce frontend et le backend fait en NodeJS ont été développés à des fins de travail pratique pour la discipline de Programmation Mobile Multiplateforme du cours Techniques de l'Informatique du Collège André-Grasset." 
                        class="welcome-content" 
                        editable="false" 
                    />
                    <Button text="Get Started" @tap="goToNextPage" class="action-button" />
                </StackLayout>
            </StackLayout>
        </ScrollView>
    </Page>
</template>

<script>
import Register from "./Register.vue"; 

export default {
    methods: {
        goToNextPage() {
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
</style>
