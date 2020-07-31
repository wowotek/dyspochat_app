<template>
    <div style="margin-left: 15%; margin-right: 15%; margin-top: 5%;">
        <div style="margin-left: 5%; margin-right: 5%">
            <a class="button is-info is-fullwidth" style="margin-top: 1%; margin-bottom: 1%; padding: 2%"
                id="tambah" onclick="tambah()">
                <p class="has-text-white" id="tambah_text">Add Chatroom</p>
            </a>
        </div>
        <div id="menu" class="dsbl" style="transform-origin: top; transform: scale(100%, 100%);">
            <div class="box" style="padding: 0; overflow: hidden;">
                <div id="submenu" class="box my-0" style="padding: 3%;">
                    <div class="field is-grouped is-grouped-centered">
                        <p class="control">
                            <a class="button is-info" onclick="create_chatroom_menu()">Create</a>
                        </p>
                        <p class="control">
                            <a class="button is-success" onclick="join_chatroom_menu()">Join</a>
                        </p>
                    </div>
                </div>
                <div id="new_menu" class="box has-background-info my-0 dsbl" style="padding: 3%;">
                    <p class="has-text-centered">Create New Chatroom ?</p>
                    <div class="field is-grouped is-grouped-centered">
                        <p class="control">
                            <a class="button is-success" v-on:click="create_chatroom">Yes</a>
                        </p>
                        <p class="control">
                            <a class="button is-danger" onclick="cancel_new()">Cancel</a>
                        </p>
                    </div>
                </div>
                <div id="join_menu" class="box has-background-success my-0 dsbl" style="padding: 3%;">
                    <p>Chat ID</p>
                    <input v-model="chat_id" type="text" class="input">
                    <div class="field is-grouped is-grouped-right">
                        <p class="control">
                            <a class="button is-info" v-on:click="join_chatroom">Join</a>
                        </p>
                        <p class="control">
                            <a class="button is-danger" onclick="cancel_join()">Cancel</a>
                        </p>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
  name: 'RoomlistMenu',
  props: {
      user: Object
  },
  data: function(){
      return {
          processing: false,
          chat_id: "",
      }
  },
  methods: {
      create_chatroom: function(){
          this.processing = true;
          this.axios
            .put("http://34.101.203.39:5000/chatroom", {}, {
                headers: {'x-api-key': 'wowotek-key'}
            })
            .then(response => {
                this.processing = false;
                if (response.data.status == "success"){
                    this.message = "Successfully created chatroom";
                    this.message_class = "has-background-success";
                    this.$emit("menu_action", "add", {id: response.data.chatroom.id, recipients: [], chats: []});
                } else {
                    this.message = "Failed to create chatroom";
                    this.message_class = "has-background-warning";
                }
            })
            .catch(error => {
                error;
                this.message = "Unexpected error, try again";
                this.message_class = "has-background-danger";
                this.processing = false;
            });
      },
      join_chatroom: function(){
          this.processing = true;
          this.axios
            .post("http://34.101.203.39:5000/chatroom", {
                chatroom_id: this.chat_id,
                recipient_id: this.user.id
            },{
                headers: {'x-api-key': 'wowotek-key'}
            })
            .then(response => {
                this.processing = false;
                if (response.data.status == "success"){
                    this.message = "Successfully joined chatroom";
                    this.message_class = "has-background-success";
                    this.$emit("menu_action", "join", {id: this.chat_id});
                } else if (response.data.status == "recipient_already_exist") {
                    this.message = "You already joined chatroom";
                    this.message_class = "has-background-warning";
                    this.$emit("menu_action", "join", {id: this.chat_id});
                } else {
                    this.message = "Failed to join chatroom";
                    this.message_class = "has-background-warning";
                }
            })
            .catch(error => {
                error;
                this.message = "Unexpected error, try again";
                this.message_class = "has-background-danger";
                this.processing = false;
            });
      },
  }
}
</script>