<template>
    <Page>
        <ActionBar title="Posts" />
        <StackLayout class="posts-container">
            <Label text="Posts List" class="title" />
            <ListView 
                for="post in posts" 
                class="post-list" 
                @itemTap="viewPostDetails"
            >
                <v-template>
                    <StackLayout class="post-item">
                        <Label :text="'User ID: ' + post.user_id" class="post-user" />
                        <Label :text="post.content" class="post-content" />
                    </StackLayout>
                </v-template>
            </ListView>
            <Button text="Create Post" @tap="goToCreatePost" class="button" />
            <Button text="Go Back" @tap="goBack" class="button-back" />
        </StackLayout>
    </Page>
</template>

<script>
import axios from "axios";
import { goBack } from "@nativescript/core/ui/frame/frame-common";
import CreatePost from "./CreatePost.vue";
import PostDetails from "./PostDetails.vue";
import * as applicationSettings from "@nativescript/core/application-settings";

export default {
    data() {
        return {
            posts: [],
        };
    },
    mounted() {
        this.getAllPosts();
    },
    methods: {
        async getAllPosts() {
            try {
                const response = await axios.get("http://10.0.2.2:3000/posts", {
                    headers: {
                        Authorization: `Bearer ${applicationSettings.getString("authToken")}`,
                    },
                });
                this.posts = response.data;
            } catch (error) {
                console.error(error);
            }
        },
        viewPostDetails(args) {
            const selectedPost = this.posts[args.index];
            this.$navigateTo(PostDetails, { props: { post: selectedPost } });
        },
        goToCreatePost() {
            this.$navigateTo(CreatePost);
        },
        goBack() {
            this.$navigateBack();
        },
    },
};
</script>

<style scoped>
.posts-container {
    padding: 20;
}

.title {
    font-size: 24;
    font-weight: bold;
    text-align: center;
    margin-bottom: 20;
}

.post-list {
    margin: 10;
}

.post-item {
    padding: 10;
    margin: 5;
    border-width: 1;
    border-color: #ccc;
    border-radius: 5;
}

.post-user {
    font-size: 18;
    font-weight: bold;
    margin-bottom: 5;
}

.post-content {
    font-size: 16;
    color: #666;
}

.button {
    margin-top: 20;
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
