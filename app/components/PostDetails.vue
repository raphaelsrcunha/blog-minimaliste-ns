<template>
    <Page>
        <ActionBar title="Post Details" />
        <StackLayout class="post-details-container">
            <!-- Exibe as informações do post -->
            <Label :text="post.user + ': '" class="post-user" />
            <Label :text="post.content" class="post-content" />

            <!-- Campo para inserir uma resposta -->
            <TextView 
                v-model="newResponse" 
                hint="Write your response..." 
                class="textarea" 
                multiline="true"
                editable="true"
            />
            <Button 
                text="Post Response" 
                @tap="postResponse" 
                class="button"
            />

            <Button text="Go Back" @tap="goBack" class="button-back" />

            <!-- Lista de respostas -->
            <Label text="Responses:" class="responses-title" />
            <ListView for="response in responses" class="responses-list">
                <v-template>
                    <StackLayout class="response-item">
                        <Label :text="response.user + ': '" class="response-user" />
                        <Label :text="response.content" class="response-content" />
                    </StackLayout>
                </v-template>
            </ListView>

        </StackLayout>
    </Page>
</template>

<script>
export default {
    props: {
        post: {
            type: Object,
            required: true,
        },
    },
    data() {
        return {
            newResponse: "", // Campo para o texto da resposta
            responses: [], // Lista de respostas
        };
    },
    methods: {
        postResponse() {
            if (this.newResponse.trim() !== "") {
                // Adiciona uma nova resposta à lista
                this.responses.push({
                    user: "Current User", // Substituir pelo username real do usuário logado
                    content: this.newResponse,
                });
                // Limpa o campo de texto
                this.newResponse = "";
            } else {
                alert("Please write a response before posting.");
            }
        },
        goBack() {
            this.$navigateBack();
        },
    },
};
</script>

<style scoped>
.post-details-container {
    padding: 20;
}

.post-user, .responses-title {
    font-size: 18;
    font-weight: bold;
    margin-bottom: 10;
}

.post-content, .response-content {
    font-size: 16;
    color: #333;
    margin-bottom: 20;
}

.response-input {
    margin-bottom: 15;
    padding: 10;
    font-size: 16;
    border-width: 1;
    border-color: #ccc;
    border-radius: 5;
}

.textarea {
    height: 150; /* Altura maior para textos longos */
    margin-bottom: 15;
    padding: 10;
    font-size: 18;
    border-width: 1;
    border-color: #ccc;
    border-radius: 5;
}

.responses-list {
    margin: 10;
    max-height: 300; /* Ajuste o tamanho máximo da lista */
}

.response-item {
    padding: 10;
    margin: 5;
    border-width: 1;
    border-color: #ddd;
    border-radius: 5;
}

.response-user {
    font-size: 16;
    font-weight: bold;
    margin-bottom: 5;
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
