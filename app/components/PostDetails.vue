<template>
    <Page>
        <ActionBar title="Post Details" />
        <StackLayout class="post-details-container">

            <Label :text="'Post ID: ' + post.id" class="post-user" />
            <Label :text="'User ID: ' + post.user_id" class="post-user" />
            <Label :text="'Created at: ' + formattedDate" class="post-user" />
            <Label :text=post.content class="post-content" textWrap="true" />

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

            <Button 
                text="Delete Post" 
                @tap="deletePost" 
                class="button-back" 
                :isEnabled="canDeletePost"
            />

            <Button 
                text="Go Back" 
                @tap="goBack" 
                class="button-back" 
            />

            <Label text="Comments for this post" class="responses-title" />
            <ListView for="response in responses" class="responses-list" @itemTap="viewCommentDetails">
                <v-template>
                    <StackLayout class="response-item">
                        <Label :text="'User ID: ' + response.user_id" class="response-user" />
                        <Label :text="'Created at: ' + response.created_at" class="response-user" />
                        <Label :text="response.content" class="response-content" />
                    </StackLayout>
                </v-template>
            </ListView>

        </StackLayout>
    </Page>
</template>

<script>
import axios from "axios";
import CommentDetails from "./CommentDetails.vue";
import * as applicationSettings from "@nativescript/core/application-settings";

export default {
    props: {
        post: {
            type: Object,
            required: true,
        },
    },
    data() {
        return {
            newResponse: "", 
            responses: [], 
        };
    },
    computed: {
        canDeletePost() {
            return applicationSettings.getString('userIdLogged') == this.post.user_id.toString();
        },
        formattedDate() {
            const date = new Date(this.post.created_at);
            return date.toLocaleString(); 
        }
    },
    mounted() {
        this.getAllResponses();
    },
    methods: {
        async getAllResponses() {
            try {
                const response = await axios.get(`http://10.0.2.2:3000/posts/${this.post.id}/comments`, {
                    headers: {
                        Authorization: `Bearer ${applicationSettings.getString("authToken")}`,
                    },
                });
                this.responses = response.data;
            } catch (error) {
                alert("Failed to load responses. Try again.");
            }
        },
        postResponse() {
            if (this.newResponse) {
                axios.post(`http://10.0.2.2:3000/posts/${this.post.id}/comments`, {
                    content: this.newResponse,
                }, {
                    headers: {
                        Authorization: `Bearer ${applicationSettings.getString("authToken")}`,
                    },
                }).then((response) => {
                    if (response && response.status === 201) {
                        alert("Response posted successfully!");
                        this.newResponse = "";
                        this.getAllResponses();
                    } else {
                        alert("Failed to post the response. Try again.");
                    }
                }).catch((error) => {
                    console.error(error);
                });
            }
        },
        async deletePost() {
            const response = await axios.delete(`http://10.0.2.2:3000/posts/${this.post.id}`, {
                headers: {
                    Authorization: `Bearer ${applicationSettings.getString("authToken")}`,
                },
            });

            if (response && response.status === 201) {
                alert("Post deleted successfully!");
                this.$emit("postDeleted");
                this.$navigateBack();
            } else {
                alert("Failed to delete the post. Try again.");
            }
        },
        goBack() {
            this.$navigateBack();
        },
        viewCommentDetails(args) {
            const selectedResponse = this.responses[args.index];
            this.$navigateTo(CommentDetails, { 
                props: { comment: selectedResponse },
                events: {
                    commentDeleted: this.getAllResponses,
                    commentUpdated: this.getAllResponses
                }
            });
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
    width: 100%;
    color: #333;
    margin-bottom: 20;
    text-wrap: true;
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
    height: 150; 
    margin-bottom: 15;
    padding: 10;
    font-size: 18;
    border-width: 1;
    border-color: #ccc;
    border-radius: 5;
}

.responses-list {
    margin: 10;
    max-height: auto; 
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
