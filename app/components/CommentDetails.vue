<template>
    <Page>
        <ActionBar title="Comment Details" flat="true" class="action-bar">
            <NavigationButton text="Back" android.systemIcon="ic_menu_back" @tap="goBack" />
        </ActionBar>
        <ScrollView>
            <StackLayout class="comment-details-container">
                <StackLayout class="comment-item">
                    <FlexboxLayout class="comment-header">
                        <Label text="@" class="user-icon" />
                        <Label :text="'user_' + comment.user_id" class="comment-user" />
                    </FlexboxLayout>
                    
                    <TextView 
                        v-model="newResponse" 
                        class="textarea" 
                        multiline="true"
                        editable="true"
                    />

                    <FlexboxLayout class="buttons-container">
                        <Button 
                            text="Update" 
                            @tap="updateResponse" 
                            class="action-button"
                        />
                        
                        <Button 
                            text="Delete" 
                            @tap="deleteComment" 
                            class="delete-button" 
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
        comment: {
            type: Object,
            required: true,
        },
    },
    data() {
        return {
            newResponse: this.comment.content, 
        };
    },
    mounted() {
        const userIdLogged = applicationSettings.getString('userIdLogged');
        if (userIdLogged != this.comment.user_id.toString()) {
            alert("Access denied");
            this.$navigateBack();
        }
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
.action-bar {
    background-color: #ffffff;
    color: #1a1a1a;
}

.comment-details-container {
    padding: 0;
    background-color: #f8f9fa;
}

.comment-item {
    padding: 16;
    margin: 8;
    background-color: white;
    elevation: 2;
}

.comment-header {
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

.comment-user {
    font-size: 16;
    font-weight: 500;
    color: #1a1a1a;
    padding-top: 6;
}

.textarea {
    height: 120;
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

.delete-button {
    width: 45%;
    background-color: #dc3545;
    color: white;
    font-size: 16;
    font-weight: bold;
    padding: 12;
    border-radius: 8;
}
</style>