<template>
  <div id="app">
    <div class="modal" v-bind:class="[!authenticated ? 'is-active' : '']">
      <div class="modal-background"></div>
      <div class="modal-content">
        <Login v-if="!authenticated" v-on:authenticated="setAuthenticated"/> 
      </div>
    </div>
    <div style="height: 10rem; background-color: rgb(165, 160, 143);"></div>
    <div style="height: 100%" class="has-background-grey-lighter"></div>
    <div>
      <span style="position: absolute; bottom: 0.5%; left: 50%; transform: translate(-50%, 0);">
        <small><sub>© 2005-2020 Kuycompany. All rights reserved.</sub></small>
      </span>
    </div>
    <div style="position: absolute; top: 5%; left: 5%; width: 90%; height: 90%;" v-bind:class="[!authenticated ? 'blurred' : '']">
      <div class="box" style="height: 100%; padding: 0; box-shadow: 0 0 15px 0.2px lightgoldenrodyellow">
        <div class="columns" style="height: 100%; padding: 0; margin: 0">
          <div class="column is-one-fifth has-background-dark" style="padding: 0;">
            <section style="height: 100%">
              <section class="has-background-dark" style="margin:0; padding: 0; height: 13%;">
                <!-- <img src="./assets/logo.png" alt="Logo" width="150" height="" style="margin-left: 25%; padding: 0; margin-top: 4%"> -->
                <h3 class="subtitle is-6 has-text-centered" v-bind:class="message_class" v-if="message" >{{ message }}</h3>
                <h1 class="title is-5 has-text-centered has-text-white" style="margin-top: 5%">{{ user.username }} + {{ session.id }}</h1>
                <h1 class="subtitle is-5 has-text-centered has-text-white">{{ user.pseudonym }}</h1>
              </section>
              <section class="has-background-light" style="padding: 1%">
                <div class="field">
                  <p class="control has-icons-left">
                    <input class="input" type="text" placeholder="Search For Chat" v-model="search_bar" v-on:change="search" @input="search"/>
                    <span class="icon is-small is-left">
                      <i class="fas fa-search"></i>
                    </span>
                  </p>
                </div>
              </section>
              <section style="margin: 1%">
                <ChatroomButton v-if="!this.search_bar" :chatrooms="chatrooms" v-on:open_chatroom="this.openChatroom"/>
                <ChatroomButton v-else :chatrooms="search_chatrooms" v-on:open_chatroom="this.openChatroom"/>
                <hr>
                <Roomlistmenu v-on:menu_action="handleMenuAction" :user="user"/>
              </section>
            </section>
          </div>
          <div class="column has-background-grey" style="padding: 0">
            <section v-if="chatroom == null" class="has-background-warning" style="height: 100%">
              <h1 class="title has-text-centered" style="padding-top: 10vw">Select, Create, or Join Chatroom</h1>
            </section>
            <section v-else style="height: 100%">
                <section class="has-background-dark" style="margin:0; padding-left: 2%; height: 8%; border-left: 1px solid grey">
                  <article class="media">
                    <div class="media-content">
                      <ChatroomInfo :chatroom="chatroom"/>
                    </div>
                    <div class="media-right" style="padding-right: 1%">
                      <div class="field has-addons">
                        <p class="control">
                          <button class="button is-danger" style="margin-top:15%" v-on:click="delete_room(chatroom.id)">
                            <span class="icon">
                              <i class="fas fa-trash"></i>
                            </span>
                            <span>Delete</span>
                          </button>
                        </p>
                        <p class="control">
                          <button class="button is-warning" style="margin-top:15%" v-on:click="leave_room(chatroom.id)">
                            <span class="icon">
                              <i class="fas fa-sign-out-alt"></i>
                            </span>
                            <span>Leave</span>
                          </button>
                        </p>
                      </div>
                    </div>
                  </article>
                </section>
                <section style="height: 84%; padding: 1%; overflow: auto">
                  <MessageBubble :messages="chatroom.chats" :user="user"/>
                  <div style="margin-bottom: 45%"></div>
                </section>
                <section class="has-background-dark" style="height: 8%; bottom: 0; padding: 1%; padding-top: 0.7%">
                  <MessageInput :chatroom="chatroom" :user="user" v-on:message_sent="messageSent"/>
                </section>
            </section>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import Login from "./components/Login.vue"
  import Roomlistmenu from "./components/RoomlistMenu.vue"
  import ChatroomInfo from "./components/ChatroomInfo.vue"
  import ChatroomButton from "./components/ChatroomButton.vue"
  import MessageInput from './components/MessageInput.vue'
  import MessageBubble from './components/MessageBubble.vue'
  import Config from './config'
  import Pusher from "pusher-js";

  export default {
    name: 'App',
    components: {
      Login,
      Roomlistmenu,
      ChatroomInfo,
      ChatroomButton,
      MessageInput,
      MessageBubble
    },
    data: function () {
      var datas = {
        authenticated: false,
        user: {},
        session: {},
        chatroom: null,
        chatrooms: [],
        search_chatrooms: [],
        message: "",
        message_class: "",
        pusher: null,
        recipient_notifications: null,
        chat_notifications: null,
        search_bar: ""
      }
      return datas;
    },
    methods: {
      async sortMessage(messages){
        let len = messages.length;
        for (let i = 0; i < len; i++) {
            let min = i;
            for (let j = i + 1; j < len; j++) {
                if (messages[j].timestamp > messages[min].timestamp) {
                    min = j;
                }
            }
            if (min !== i) {
                let tmp = messages[i];
                messages[i] = messages[min];
                messages[min] = tmp;
            }
        }
        return messages;
      },
      async messageSent(chatroom_id){
        this.chatroom.chats = await this.sortMessage(this.chatroom.chats);
        await this.openChatroom(chatroom_id);
      },
      async search(evt){
        console.log("searching for " + this.search_bar);
        var match = [];
        function add_to_match(adder){
          for(var k=0; k<match.length; k++){
            if(match[k].id == adder.id) return;
          }
          match.push(adder)
        }

        if(this.search_bar != ""){
          for(var i=0; i<this.chatrooms.length; i++){
            var cr = this.chatrooms[i];
            try{
              var intSearch = parseInt(this.search_bar);
              if(cr.id == intSearch){
                add_to_match(cr);
              }
            } catch {evt;}

            for(var j=0; j<cr.chats.length; j++){
              var crc = cr.chats[j];
              if(crc.message == this.search_bar){
                add_to_match(cr);
              }
              if(crc.message.includes(this.search_bar)){
                add_to_match(cr);
              }
              console.log(crc.message);
            }
          }
        }
      },
      async delete_room(room_id){
        this.axios
          .post(Config.HOST + "/chatroom/delete",{
              chatroom_id: room_id
            }, {
              headers: {'x-api-key': 'wowotek-key'}
          })
          .then(response => {
              this.processing = false;
              if (response.data.status == "success"){
                  this.message = "Successfully deleting chatroom";
                  this.message_class = "has-background-success";
                  this.chatroom = null;
                  for(var i=0; i<this.chatrooms.length; i++){
                    if(this.chatrooms[i].id == room_id) {
                      this.chatrooms.splice(i, 1);
                      return;
                    }
                  }
              } else {
                  this.message = "Chatroom Not Found !";
                  this.message_class = "has-background-danger";
              }
          })
          .catch(error => {
              error;
              this.message = "Unexpected error, try again";
              this.message_class = "has-background-danger";
              this.processing = false;
        });
      },
      async leave_room(room_id){
        this.axios
          .post(Config.HOST + "/chatroom/recipient/delete",{
              chatroom_id: room_id,
              recipient_id: this.user.id
            }, {
              headers: {'x-api-key': 'wowotek-key'}
          })
          .then(response => {
              this.processing = false;
              if (response.data.status == "success"){
                  this.message = "Successfully deleting chatroom";
                  this.message_class = "has-background-success";
              } else {
                  this.message = "Chatroom Not Found !";
                  this.message_class = "has-background-danger";
              }
          })
          .catch(error => {
              error;
              this.message = "Unexpected error, try again";
              this.message_class = "has-background-danger";
              this.processing = false;
        });
        for(var i=0; i<this.chatrooms.length; i++){
          if(this.chatrooms[i].id == room_id){
            this.chatrooms.splice(i);
            this.chatroom = null;
            return;
          }
        }
        console.log("Leaving Chatroom");
        console.log(this.chatroom);
      },
      async push_notification(notification_message){
        console.log(notification_message);
      },
      async message_update(chatroom_id){
        this.axios
          .post(Config.HOST + "/chatroom/info",{
              chatroom_id: chatroom_id
            }, {
              headers: {'x-api-key': 'wowotek-key'}
          })
          .then(response => {
              this.processing = false;
              if (response.data.status == "success"){
                  this.message = "Successfully updating chatroom";
                  this.message_class = "has-background-success";
                  this.chatroom = response.data.chatroom;
              } else {
                  this.message = "Chatroom Not Found !";
                  this.message_class = "has-background-danger";
              }
          })
          .catch(error => {
              error;
              this.message = "Unexpected error, try again";
              this.message_class = "has-background-danger";
              this.processing = false;
        });
      },
      async openChatroom(chatroom_id){
        for(var i=0; i<this.chatrooms.length; i++){
          if(this.chatrooms[i].id == chatroom_id){
            this.axios
              .post(Config.HOST + "/chatroom/info",{
                  chatroom_id: chatroom_id
                }, {
                  headers: {'x-api-key': 'wowotek-key'}
              })
              .then(response => {
                  this.processing = false;
                  if (response.data.status == "success"){
                      this.message = "Successfully updating chatroom";
                      this.message_class = "has-background-success";
                      this.chatroom = response.data.chatroom;
                      this.chatroom.chats = this.sortMessage(this.chatroom.chats);
                  } else {
                      this.message = "Chatroom Not Found !";
                      this.message_class = "has-background-danger";
                  }
              })
              .catch(error => {
                  error;
                  this.message = "Unexpected error, try again";
                  this.message_class = "has-background-danger";
                  this.processing = false;
            });
          }
        }
      },
      async setAuthenticated(login_status, session, user){
        this.authenticated = true;
        this.session = {
          id: session.id,
          key: session.key
        };
        this.user = {
          id: user.id,
          username: user.username,
          pseudonym: user.pseudonym
        }

        this.pusher = new Pusher('a41cf1ede332504cd58e', {
          app_id: '1045271',
          cluster: 'ap1',
          key: 'a41cf1ede332504cd58e',
          secret: '5ca06be10cf15aa08d86'
        });
      },
      async handleMenuAction(action, chatroom_data){
        this.recipient_notifications = this.pusher.subscribe(
          chatroom_data.id.toString()
        );

        this.recipient_notifications.bind("new_recipient", data => {
          if(data.recipient.id === this.user.id){
            this.push_notification("You Joined your chatroom id: " + data.chatroom.id);
          } else {
            this.push_notification(data.recipient.pseudonym + " Joined your chatroom id: " + data.chatroom.id);
          }
          this.message_update(data.chatroom.id);
        });

        this.recipient_notifications.bind("del_recipient", data => {
          if(data.recipient.id === this.user.id){
            this.push_notification("You Are Leaving chatroom id: " + data.chatroom.id);
          } else {
            this.push_notification(data.recipient.pseudonym + " Leaving chatroom id: " + data.chatroom.id);
          }
        });

        this.chat_notifications = this.pusher.subscribe(
          chatroom_data.id.toString()
        );

        this.chat_notifications.bind("new_chat", data => {
          this.push_notification("New chat from " + data.chat.sender.pseudonym + "in chatroom id " + data.chatroom.id)
          this.message_update(data.chatroom.id);
        });

        this.chat_notifications.bind("del_chat", data => {
          this.push_notification("Someone deleted chatroom id " + data.chatroom.id);
        });
        
        if(action == "add"){  // Add Chatroom
          this.axios          // Join Chatroom after creating it
            .post(Config.HOST + "/chatroom/recipient/add", {
                chatroom_id: chatroom_data.id,
                recipient_id: this.user.id
            },{
                headers: {'x-api-key': 'wowotek-key'}
            })
            .then(response => {
                this.processing = false;
                if (response.data.status == "success"){
                    this.message = "Successfully joined chatroom";
                    this.message_class = "has-background-success";

                    console.log("GET Chatroominfo");
                    console.log(chatroom_data.id);
                    this.axios
                      .post(Config.HOST + "/chatroom/info",{
                          chatroom_id: chatroom_data.id
                        }, {
                          headers: {'x-api-key': 'wowotek-key'}
                      })
                      .then(response => {
                          this.processing = false;
                          if (response.data.status == "success"){
                              this.message = "Successfully updating chatroom";
                              this.message_class = "has-background-success";
                              var rm = response.data.chatroom;
                              rm.chats = this.sortMessage(this.chatroom.chats);
                              this.chatrooms.push(rm);
                              this.chatroom = rm;
                          } else {
                              this.message = "Chatroom Not Found !";
                              this.message_class = "has-background-danger";
                          }
                      })
                      .catch(error => {
                          error;
                          this.message = "[handleMenuAction][add][chat_info] Unexpected error, try again";
                          this.message_class = "has-background-danger";
                          this.processing = false;
                    });
                } else if (response.data.status == "recipient_already_exist") {
                    this.message = "You already joined chatroom";
                    this.message_class = "has-background-warning";
                } else {
                    this.message = "Failed to join chatroom";
                    this.message_class = "has-background-warning";
                }
            })
            .catch(error => {
                error;
                this.message = "[handleMenuAction][add] Unexpected error, try again";
                this.message_class = "has-background-danger";
                this.processing = false;
            });
        } else {
          this.axios
            .post(Config.HOST + "/chatroom/info",{
                chatroom_id: chatroom_data.id
              }, {
                headers: {'x-api-key': 'wowotek-key'}
            })
            .then(response => {
                this.processing = false;
                if (response.data.status == "success"){
                    if(this.chatrooms.includes(response.data.chatroom)) return;
                    for(var i=0; i<this.chatrooms.length; i++){
                      if(this.chatrooms[i].id == response.data.chatroom.id){
                        return;
                      }
                    }
                    var rm = response.data.chatroom;
                    rm.chats = this.sortMessage(this.chatroom.chats);
                    this.chatrooms.push(rm);
                } else {
                    this.message = "Chatroom Not Found !";
                    this.message_class = "has-background-danger";
                }
            })
            .catch(error => {
                error;
                this.message = "[handleMenuAction][else] Unexpected error, try again";
                this.message_class = "has-background-danger";
                this.processing = false;
          });
        }
      }
    }
  }
</script>

<style scoped>
.blurred {
  -webkit-filter: blur(15px);
  -moz-filter: blur(15px);
  -ms-filter: blur(15px);
  -o-filter: blur(15px);
  filter: blur(15px);
}
</style>