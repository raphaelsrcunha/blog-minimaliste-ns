<template>
    <Page>
        <ActionBar title="Create Post" />
        <StackLayout class="form-container">
            <TextView 
                v-model="content" 
                hint="Write your post here..." 
                class="textarea"
                editable="true"
            />
            <Button 
                text="Submit Post" 
                @tap="submitPost" 
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
import * as applicationSettings from "@nativescript/core/application-settings";

export default {
    props: {
        onPostCreated: {
            type: Function,
            default: null
        }
    },
    data() {
        return {
            content: "", 
        };
    },
    methods: {
        async submitPost() {
            if (this.content.trim()) {
                try {
                    const response = await axios.post("http://10.0.2.2:3000/posts", {
                        content: this.content,
                    }, {
                        headers: {
                            Authorization: `Bearer ${applicationSettings.getString("authToken")}`,
                        },
                    });

                    if (response && response.status === 201) {
                        alert("Post created successfully!");
                        
                        if (this.onPostCreated) {
                            this.onPostCreated();
                        }
                        
                        this.clearFields();
                        this.$navigateBack(); 
                    } else {
                        alert("Failed to create the post. Try again.");
                    }
                } catch (error) {
                    console.error(error);
                    alert("An error occurred. Please try again.");
                }
            } else {
                alert("Please write something before submitting.");
            }
        },
        clearFields() {
            this.content = "";
        },
        goBack() {
            this.$navigateBack();
        },
    },
};
</script>

<style scoped>
.form-container {
    padding: 20;
    margin: 20;
}

.textarea {
    height: 150;
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