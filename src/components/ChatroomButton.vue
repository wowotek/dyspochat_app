<template>
  <div>
    <a class="box has-background-info-light" style="margin-top: 1%; margin-bottom: 1%; padding: 2%" v-for="chatroom in chatrooms" v-bind:key="chatroom.id" v-on:click="openChatroom($event, chatroom.id)">
      <p>{{ chatroom.id }}</p>
      <span>
          <div class="tag is-success" v-for="recipient in chatroom.recipients" v-bind:key="recipient.id">
              {{ recipient.pseudonym }}
          </div>
      </span>
    </a>
  </div>
</template>

<script>
export default {
  name: 'ChatroomButton',
  props: {
    chatrooms: Array
  },
  methods: {
    openChatroom: function(event, id){
      console.log("Opening Chatroom" + id);
      this.$emit("open_chatroom", id);
    }
  }
};
</script>