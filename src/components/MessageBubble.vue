<template>
    <div>
        <div v-for="(message, msg_id) in messages" v-bind:key="msg_id">
            <div v-bind:class="[(message.sender.id == user.id) ? 'msg_fp' : 'msg_op']">
                <b><sub class="has-text-warning">{{ message.sender.pseudonym }}</sub></b>
                <sub><small class="has-text-white">{{ "  " + timestamp_datetime(message.timestamp) }}</small></sub>
                <div class="box has-background-success-light msg">
                    {{ message.message }}
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
  name: 'MessageBubble',
  props: {
    messages: Array,
    user: Object
  },
  methods: {
    timestamp_datetime: function(datetime){
      var date = new Date(datetime * 1000);
      var hours = date.getHours();
      var minutes = "0" + date.getMinutes();
      var seconds = "0" + date.getSeconds();
      return hours + ':' + minutes.substr(-2) + ':' + seconds.substr(-2);
    }
  }
}
</script>