<template>
    <Page>
        <ActionBar :title="translations[currentLanguage].posts.title" flat="true" class="action-bar">
            <NavigationButton text="Back" android.systemIcon="ic_menu_close_clear_cancel" @tap="goBack" />
        </ActionBar>
        <GridLayout rows="auto,*" columns="*">
            <Label :text="translations[currentLanguage].posts.greeting + ', ' + getUsername() + ' :)'" class="greeting" row="0" col="0" />
            <StackLayout class="posts-container" row="1" col="0">
                <ListView for="post in posts" class="post-list" separatorColor="transparent" @itemTap="viewPostDetails">
                    <v-template>
                        <StackLayout class="post-item">
                            <FlexboxLayout class="post-header">
                                <Label text="@" class="user-icon" />
                                <Label :text="'user_' + post.user_id" class="post-user" />
                            </FlexboxLayout>
                            <Label :text="post.content" class="post-content" textWrap="true" />
                        </StackLayout>
                    </v-template>
                </ListView>
            </StackLayout>
            <Button text="+" @tap="goToCreatePost" class="fab-button" row="1" col="0" />
        </GridLayout>
    </Page>
</template>

<script>
import axios from "axios";
import CreatePost from "./CreatePost.vue";
import PostDetails from "./PostDetails.vue";
import { translations } from '../i18n/translations';
import * as applicationSettings from "@nativescript/core/application-settings";

export default {
    data() {
        return {
            posts: [],
            translations,
            currentLanguage: 'fr'
        };
    },
    created() {
        this.currentLanguage = applicationSettings.getString('appLanguage', 'fr');
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
                alert(this.translations[this.currentLanguage].posts.loadError);
            }
        },
        viewPostDetails(args) {
            try {
                const selectedPost = this.posts[args.index];
                this.$navigateTo(PostDetails, { 
                    props: { post: selectedPost },
                    events: {
                        postDeleted: this.getAllPosts
                    }
                });
            } catch (error) {
                alert(error);
            }
        },
        goToCreatePost() {
            this.$navigateTo(CreatePost, {
                props: {
                    onPostCreated: this.getAllPosts
                }
            });
        },
        goBack() {
            this.$navigateBack();
        },
        getUsername() {
            return applicationSettings.getString('usernameLogged');
        },
    },
};
</script>

<style scoped>
.action-bar {
    background-color: #ffffff;
    color: #1a1a1a;
}

.posts-container {
    padding: 0;
    background-color: #f8f9fa;
}

.fab-button {
    height: 56;
    width: 56;
    margin: 16;
    border-radius: 28;
    font-size: 24;
    font-weight: bold;
    background-color: #1a73e8;
    color: white;
    horizontal-align: right;
    vertical-align: bottom;
    elevation: 6;
}

.post-list {
    margin: 0;
    background-color: transparent;
}

.post-item {
    padding: 16;
    margin: 8 0;
    background-color: white;
    border-radius: 0;
    elevation: 1;
}

.post-header {
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

.post-content {
    font-size: 16;
    color: #333333;
    line-height: 1.5;
    margin-bottom: 8;
}

.greeting {
    font-size: 18;
    font-weight: 400;
    color: #000000;
    padding: 16;
    background-color: white;
    border-bottom-width: 1;
    border-bottom-color: #e0e0e0;
    text-align: center;
}
</style>