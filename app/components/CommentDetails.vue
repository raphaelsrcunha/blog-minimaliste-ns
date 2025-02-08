<template>
    <Page>
        <ActionBar title="Comment Details" class="action-bar">
            <NavigationButton text="Back" @tap="goBack" android.systemIcon="ic_menu_back" />
        </ActionBar>
        <StackLayout class="page">
            <Label :text="'User ID: ' + comment.user_id" class="comment-user" />
            <Label :text="'Created at: ' + comment.created_at" class="comment-date" />
            <Label :text="comment.content" class="comment-content" />

            <TextView 
                v-model="newResponse" 
                hint="Write your response..." 
                class="textarea" 
                multiline="true"
                editable="true"
            />

            <Button 
                text="Update Response" 
                @tap="updateResponse" 
                class="button"
            />
            
            <Button 
                text="Delete" 
                @tap="deleteComment" 
                class="delete-button" 
            />
        
        </StackLayout>



    </Page>
</template>

<script>
import axios from "axios";
import * as applicationSettings from "@nativescript/core/application-settings";

export default {
    props: {
        comment: {
            type: Object,
            required: true,
        },
    },
    data() {
        return {
            newResponse: "",
        };
    },
    methods: {
        goBack() {
            this.$navigateBack();
        },
        async deleteComment() {
            const response = await axios.delete(`http://10.0.2.2:3000/comments/${this.comment.id}`, {
                headers: {
                    Authorization: `Bearer ${applicationSettings.getString("authToken")}`,
                },
            });
            if (response && response.status === 204) {
                alert("Comment deleted successfully");
                this.$emit("commentDeleted");
                this.$navigateBack();
            } else {
                alert("Failed to delete comment");
            }
        },
        async updateResponse() {
            const response = await axios.put(`http://10.0.2.2:3000/comments/${this.comment.id}`, {
                content: this.newResponse,
            }, {
                headers: {
                    Authorization: `Bearer ${applicationSettings.getString("authToken")}`,
                },
            });
            if (response && response.status === 204) {
                alert("Comment updated successfully");
                this.$emit("commentUpdated");
                this.$navigateBack();
            } else {
                alert("Failed to update comment");
            }
        },
    },
};
</script>

<style scoped>
.page {
    padding: 20;
}

.comment-user {
    font-size: 20;
    margin-bottom: 10;
}

.comment-date {
    font-size: 16;
    margin-bottom: 10;
}

.comment-content {
    font-size: 16;
}

.delete-button {
    margin-top: 10;
    padding: 10;
    font-size: 18;
    background-color: #f44336;
    color: white;
    text-align: center;
    border-radius: 5;
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
</style>