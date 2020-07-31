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
                    <input class="input" type="text" placeholder="Search For Chat">
                    <span class="icon is-small is-left">
                      <i class="fas fa-search"></i>
                    </span>
                  </p>
                </div>
              </section>
              <section style="margin: 1%">
                <ChatroomButton :chatrooms="chatrooms" v-on:open_chatroom="openChatroom"/>
                <hr>
                <Roomlistmenu v-on:menu_action="handleMenuAction" :user="user"/>
              </section>
            </section>
          </div>
          <div class="column has-background-grey" style="padding: 0">
            <section v-if="chatroom" style="height: 100%">
                <section class="has-background-dark" style="margin:0; padding-left: 2%; height: 8%; border-left: 1px solid grey">
                  <article class="media">
                    <div class="media-content">
                      <ChatroomInfo :chatroom="chatroom"/>
                    </div>
                    <div class="media-right" style="padding-right: 1%">
                      <button class="button is-danger" style="margin-top:15%">
                        <span class="icon">
                          <i class="fas fa-trash"></i>
                        </span>
                        <span>Delete</span>
                      </button>
                    </div>
                  </article>
                </section>
                <section style="height: 84%; padding: 1%; overflow: auto">
                  <MessageBubble :messages="chatroom.chats" :user="user"/>
                  <div style="margin-bottom: 45%"></div>
                </section>
                <section class="has-background-dark" style="height: 8%; bottom: 0; padding: 1%; padding-top: 0.7%">
                  <MessageInput :chatroom="chatroom" :user="user" v-on:message_sent="message_update"/>
                </section>
            </section>
            <section v-if="!chatroom" class="has-background-warning" style="height: 100%">
              <h1 class="title has-text-centered" style="padding-top: 10vw">Select, Create, or Join Chatroom</h1>
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
  import Pusher from "pusher-js";

  

  function sortMessage(messages){
    let len = messages.length;
    for (let i = 0; i < len; i++) {
        let min = i;
        for (let j = i + 1; j < len; j++) {
            if (messages[min].timestamp > messages[j].timestamp) {
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
  }

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
        chatroom: {},
        chatrooms: [],
        message: "",
        message_class: "",
        pusher: null,
        notification: null
      }
      return datas;
    },
    methods: {
      async message_update(chatroom_id){
        this.axios({
            url: "http://localhost:5000/chatroom/"+chatroom_id,
            method: "GET",
            headers: {
              'x-api-key': 'wowotek-key',
            }
          })
          .then(response_ => {
            console.log(response_);
            if(response_.data.status == "success"){
              this.chatroom = response_.data.chatroom;
              this.chatroom.chats = sortMessage(this.chatroom.chats);
            }
          })
          .catch(error => {
            console.log(error);
            this.message = "Unexpected error, try again";
            this.message_class = "has-background-danger";
            this.processing = false;
          });
      },
      async openChatroom(chatroom_id){
        for(var i=0; i<this.chatrooms.length; i++){
          if(this.chatrooms[i].id == chatroom_id){
            this.axios({
                url: "http://localhost:5000/chatroom/"+this.chatrooms[i].id,
                method: "GET",
                headers: {
                  'x-api-key': 'wowotek-key',
                }
              })
              .then(response_ => {
                console.log(response_);
                if(response_.data.status == "success"){
                  this.chatroom = response_.data.chatroom;
                  this.chatroom.chats = sortMessage(this.chatroom.chats);
                }
              })
              .catch(error => {
                console.log(error);
                this.message = "Unexpected error, try again";
                this.message_class = "has-background-danger";
                this.processing = false;
              });
              
              console.log(this.chatroom);
              return;
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
        this.notifications = this.pusher.subscribe(
          chatroom_data.id.toString()
        );

        this.notifications.bind("new_recipient", data => {
          console.log("notification data");
          console.log(data);
          this.message = JSON.stringify(data);
        });
        if(action == "add"){  // Add Chatroom
          this.axios          // Join Chatroom after creating it
            .post("http://localhost:5000/chatroom", {
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
                    this.axios({
                        url: "http://localhost:5000/chatroom/"+chatroom_data.id,
                        method: "GET",
                        headers: {
                          'x-api-key': 'wowotek-key',
                        }
                      })
                      .then(response_ => {
                        console.log(response_);
                        if(response_.data.status == "success"){
                          var rm = response_.data.chatroom
                          rm.chats = sortMessage(rm.chats);
                          this.chatrooms.push(rm);
                        }
                      })
                      .catch(error => {
                        console.log(error);
                        this.message = "Unexpected error, try again";
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
                this.message = "Unexpected error, try again";
                this.message_class = "has-background-danger";
                this.processing = false;
            });
        } else {
          console.log("GET Chatroominfo");
          console.log(chatroom_data.id);
          this.axios({
              url: "http://localhost:5000/chatroom/"+chatroom_data.id,
              method: "GET",
              headers: {
                'x-api-key': 'wowotek-key',
              }
            })
            .then(response_ => {
              if(response_.data.status == "success"){
                if(this.chatrooms.includes(response_.data.chatroom)) return;
                this.chatrooms.push(response_.data.chatroom);
              }
            })
            .catch(error => {
              console.log(error);
              this.message = "Unexpected error, try again";
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