<template>
    <Page>
        <ActionBar title="Create Post" flat="true" class="action-bar">
            <NavigationButton text="Back" android.systemIcon="ic_menu_back" @tap="goBack" />
        </ActionBar>
        <ScrollView>
            <StackLayout class="create-post-container">
                <StackLayout class="post-form">
                    <FlexboxLayout class="form-header">
                        <Label text="@" class="user-icon" />
                        <Label :text="'user_' + getCurrentUserId()" class="post-user" />
                    </FlexboxLayout>
                    
                    <TextView 
                        v-model="content" 
                        hint="What's on your mind?" 
                        class="textarea"
                        multiline="true"
                        editable="true"
                    />

                    <FlexboxLayout class="buttons-container">
                        <Button 
                            text="Post" 
                            @tap="submitPost" 
                            class="action-button"
                        />
                        
                        <Button 
                            text="Cancel" 
                            @tap="goBack" 
                            class="cancel-button"
                        />
                    </FlexboxLayout>
                </StackLayout>
            </StackLayout>
        </ScrollView>
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
        getCurrentUserId() {
            return applicationSettings.getString('userIdLogged');
        },
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
                        this.content = "";
                        this.$navigateBack(); 
                    } else {
                        alert("Failed to create the post. Try again.");
                    }
                } catch (error) {
                    console.error(error);
                    alert("An error occurred. Please try again.");
                }
            } else {
                alert("Please write something before posting.");
            }
        },
        goBack() {
            this.$navigateBack();
        },
    },
};
</script>

<style scoped>
.action-bar {
    background-color: #ffffff;
    color: #1a1a1a;
}

.create-post-container {
    padding: 0;
    background-color: #f8f9fa;
}

.post-form {
    padding: 16;
    margin: 8;
    background-color: white;
    elevation: 2;
}

.form-header {
    margin-bottom: 12;
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

.textarea {
    height: 200;
    padding: 12;
    font-size: 16;
    border-width: 1;
    border-color: #e0e0e0;
    border-radius: 8;
    margin: 16 0;
    background-color: #f8f9fa;
}

.buttons-container {
    justify-content: space-between;
    margin-top: 16;
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

.cancel-button {
    width: 45%;
    background-color: #dc3545;
    color: white;
    font-size: 16;
    font-weight: bold;
    padding: 12;
    border-radius: 8;
}
</style>