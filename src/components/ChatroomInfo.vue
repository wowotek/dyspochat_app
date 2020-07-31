<template>
    <div class="content" style="padding-top: 0.5%">
        <h1 class="title is-4 has-text-white">{{ chatroom.id }}</h1>
        <h1 class="subtitle is-6 has-text-white">
        <span style="margin-right: 0.5%">
            Recipients:
        </span>
        <span class="tag" style="margin-right: 0.5%" v-for="recipient in chatroom.recipients" v-bind:key="recipient.id">
            {{ recipient.pseudonym }}
        </span>
        </h1>
    </div>
</template>

<script>
export default {
    name: "ChatroomInfo",
    props: {
        chatroom: Object
    }
}
</script>