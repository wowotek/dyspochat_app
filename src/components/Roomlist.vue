<template>
    <section style="height: 100%">
        <section class="has-background-dark" style="margin:0; padding: 0; height: 8%">
            <img src="../assets/logo.png" alt="Logo" width="150" height="" style="margin-left: 25%; padding: 0; margin-top: 4%">
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
            <ChatroomButton/>
            <hr>
            <Roomlistmenu/>
        </section>
    </section>
</template>

<script>
import Roomlistmenu from "./subcomp/RoomlistMenu.vue"
import ChatroomButton from "./subcomp/ChatroomButton.vue"

export default {
  name: 'Roomlist',
  components: {
      Roomlistmenu,
      ChatroomButton
  }
}
</script>