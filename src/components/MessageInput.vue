<template>
    <div class="field has-addons">
        <div class="control is-expanded">
            <input v-model="message_content" class="input is-medium" type="text" placeholder="Type a Message...">
        </div>
        <div class="control">
            <a class="button is-medium is-success" v-on:click="send_message">
                <span class="icon">
                    <i class="fas fa-paper-plane"></i>
                </span>
                <span>Send</span>
            </a>
        </div>
    </div>
</template>

<script>
import Config from '../config';

export default {
  name: 'MessageInput',
  props: {
      chatroom: Object,
      user: Object,
      message_content: String
  },
  methods: {
      send_message: function(){
        this.axios
            .post(Config.HOST + "/chat/add", {
                chatroom_id: this.chatroom.id,
                chat_sender: this.user.id,
                chat_message: this.message_content
            },{
                headers: {'x-api-key': 'wowotek-key'}
            })
            .then(response => {
                this.processing = false;
                if (response.data.status == "success"){
                    this.message = "Successfully sent your message";
                    this.message_class = "has-background-success";
                    this.$emit("message_sent", this.chatroom.id);
                } else if (response.data.status == "chat_sender_not_in_chatroom"){
                    this.message = "You Are not in chatroom" + this.chatroom.id;
                    this.message_class = "has-background-warning";
                }
            })
            .catch(error => {
                error;
                this.message = "Unexpected error, try again";
                this.message_class = "has-background-danger";
                this.processing = false;
            });
        this.$emit("message_sent", this.chatroom.id);
      }
  }
}
</script>