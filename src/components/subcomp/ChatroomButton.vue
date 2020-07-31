<template>
    <div>
        <div v-for="(chatroom, room_id) in chatrooms" v-bind:key="room_id">
            <a class="box has-background-info-light" style="margin-top: 1%; margin-bottom: 1%; padding: 2%">
                <p>{{ chatroom.id }}</p>
                <span v-for="(recipient, rec_id) in chatroom.recipients" v-bind:key="rec_id">
                    <div class="tag is-success">
                        {{ recipient.pseudonym }}
                    </div>
                </span>
            </a>
        </div>
    </div>
</template>

<script>
export default {
  name: 'ChatroomButton',
  props: {
      chatrooms: Array
  }
};
</script>