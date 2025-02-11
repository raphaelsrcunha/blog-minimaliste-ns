<template>
    <Page>
        <ActionBar :title="translations[currentLanguage].postDetails.title" flat="true" class="action-bar">
            <NavigationButton text="Back" android.systemIcon="ic_menu_back" @tap="goBack" />
        </ActionBar>
        <ScrollView>
            <StackLayout class="post-details-container">
                <!-- Post Content -->
                <StackLayout class="post-item">
                    <FlexboxLayout class="post-header">
                        <Label text="@" class="user-icon" />
                        <Label :text="'user_' + post.user_id" class="post-user" />
                        <Label :text="formattedDate" class="post-date" />
                        <Label 
                            v-if="canDeletePost" 
                            text="❌" 
                            class="delete-icon" 
                            @tap="deletePost" 
                        />
                    </FlexboxLayout>
                    <Label :text="post.content" class="post-content" textWrap="true" />
                </StackLayout>

                <!-- New Comment Section -->
                <StackLayout class="comment-input-container">
                    <TextView 
                        v-model="newResponse" 
                        :hint="translations[currentLanguage].postDetails.writeComment" 
                        class="textarea" 
                        multiline="true"
                        editable="true"
                    />
                    <Button 
                        :text="translations[currentLanguage].postDetails.commentButton" 
                        @tap="postResponse" 
                        class="action-button"
                    />
                </StackLayout>

                <!-- Comments List -->
                <Label :text="translations[currentLanguage].postDetails.commentsTitle" class="section-title" />
                <StackLayout class="comments-container">
                    <StackLayout v-for="(response, index) in responses" :key="index" class="comment-item" @tap="viewCommentDetails({index})">
                        <FlexboxLayout class="comment-header">
                            <Label text="@" class="user-icon small" />
                            <Label :text="'user_' + response.user_id" class="comment-user" />
                        </FlexboxLayout>
                        <Label :text="response.content" class="comment-content" textWrap="true" />
                    </StackLayout>
                </StackLayout>
            </StackLayout>
        </ScrollView>
    </Page>
</template>

<script>
import axios from "axios";
import CommentDetails from "./CommentDetails.vue";
import { translations } from '../i18n/translations';
import * as applicationSettings from "@nativescript/core/application-settings";
import { confirm } from "@nativescript/core/ui/dialogs";

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
            translations,
            currentLanguage: 'fr'
        };
    },
    created() {
        this.currentLanguage = applicationSettings.getString('appLanguage', 'fr');
    },
    computed: {
        canDeletePost() {
            return applicationSettings.getString('userIdLogged') == this.post.user_id.toString();
        },
        formattedDate() {
            const date = new Date(this.post.created_at);
            return date.toLocaleDateString() + ' ' + date.getHours() + ':' + date.getMinutes();
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
                alert(this.translations[this.currentLanguage].postDetails.loadError);
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
                        alert(this.translations[this.currentLanguage].postDetails.commentSuccess);
                        this.newResponse = "";
                        this.getAllResponses();
                    } else {
                        alert(this.translations[this.currentLanguage].postDetails.commentError);
                    }
                }).catch((error) => {
                    alert(this.translations[this.currentLanguage].postDetails.commentError);
                });
            }
        },
        async deletePost() {
            const confirmResult = await confirm({
                title: this.translations[this.currentLanguage].postDetails.deleteConfirmTitle,
                message: this.translations[this.currentLanguage].postDetails.deleteConfirmMessage,
                okButtonText: this.translations[this.currentLanguage].postDetails.deleteButton,
                cancelButtonText: this.translations[this.currentLanguage].postDetails.cancelButton,
                neutralButtonText: null
            });

            if (confirmResult) {
                try {
                    const response = await axios.delete(`http://10.0.2.2:3000/posts/${this.post.id}`, {
                        headers: {
                            Authorization: `Bearer ${applicationSettings.getString("authToken")}`,
                        },
                    });

                    if (response && response.status === 201) {
                        alert(this.translations[this.currentLanguage].postDetails.deleteSuccess);
                        this.$emit("postDeleted");
                        this.$navigateBack();
                    } else {
                        alert(this.translations[this.currentLanguage].postDetails.deleteError);
                    }
                } catch (error) {
                    alert(this.translations[this.currentLanguage].postDetails.deleteError);
                }
            }
        },
        goBack() {
            this.$navigateBack();
        },
        viewCommentDetails(args) {
            const selectedResponse = this.responses[args.index];
            const userIdLogged = applicationSettings.getString('userIdLogged');
            
            if (userIdLogged == selectedResponse.user_id.toString()) {
                this.$navigateTo(CommentDetails, { 
                    props: { comment: selectedResponse },
                    events: {
                        commentDeleted: this.getAllResponses,
                        commentUpdated: this.getAllResponses
                    }
                });
            } else {
                alert(this.translations[this.currentLanguage].postDetails.accessDenied);
            }
        },
    },
};
</script>

<style scoped>
.action-bar {
    background-color: #ffffff;
    color: #1a1a1a;
}

.post-details-container {
    padding: 0;
    background-color: #f8f9fa;
}

.post-item {
    padding: 16;
    margin: 8;
    background-color: white;
    elevation: 2;
}

.post-header {
    margin-bottom: 12;
    align-items: center;
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

.user-icon.small {
    width: 24;
    height: 24;
    padding: 4;
    border-radius: 12;
    font-size: 12;
}

.post-user, .comment-user {
    font-size: 16;
    font-weight: 500;
    color: #1a1a1a;
    padding-top: 6;
}

.post-date {
    font-size: 14;
    color: #666666;
    padding-top: 6;
    margin-right: 8;
}

.post-content {
    font-size: 16;
    color: #333333;
    line-height: 1.5;
    margin: 8 0;
}

.comment-input-container {
    padding: 16;
    background-color: white;
    margin-top: 8;
}

.textarea {
    height: 120;
    padding: 12;
    font-size: 16;
    border-width: 1;
    border-color: #e0e0e0;
    border-radius: 8;
    margin-bottom: 12;
    background-color: #f8f9fa;
}

.section-title {
    font-size: 18;
    font-weight: 500;
    color: #1a1a1a;
    margin: 16;
}

.comments-container {
    width: 100%;
    margin-bottom: 16;
}

.comment-item {
    padding: 12;
    margin: 4 8;
    background-color: white;
    elevation: 1;
}

.comment-content {
    font-size: 14;
    color: #333333;
    margin: 4 0;
    line-height: 1.4;
}

.action-button {
    background-color: #1a73e8;
    color: white;
    font-size: 16;
    font-weight: bold;
    padding: 12;
    border-radius: 8;
}

.delete-button {
    margin: 16;
    background-color: #dc3545;
    color: white;
    font-size: 16;
    font-weight: bold;
    padding: 12;
    border-radius: 8;
}

.delete-icon {
    font-family: "FontAwesome";
    font-size: 20;
    color: #dc3545;
    padding: 8;
    margin-left: auto;
}
</style>
