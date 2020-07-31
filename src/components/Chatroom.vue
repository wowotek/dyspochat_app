<template>
    <section style="height: 100%">
        <section class="has-background-dark" tyle="margin:0; padding-left: 2%; height: 8%; border-left: 1px solid grey">
            <article class="media">
                <div class="media-content">
                    <div class="content" style="padding-top: 0.5%">
                        <h1 class="title is-4 has-text-white">ChatID</h1>
                        <h1 class="subtitle is-6 has-text-white">
                            <span class="tag">hah</span>
                            <span class="tag">hah</span>
                            <span class="tag">hah</span>
                            <span class="tag">hah</span>
                        </h1>
                    </div>
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
            <div class="msg_op">
                <b><sub class="has-text-warning">Wowotek</sub></b>
                <sub><small class="has-text-white">16:69</small></sub>
                <div class="box has-background-success-light msg"></div>
            </div>
            <div class="msg_fp">
                <b><sub class="has-text-warning">Wowotek</sub></b>
                <sub><small class="has-text-white msg_time">16:69</small></sub>
                <div class="box has-background-warning-light msg"></div>
            </div>
            <div style="margin-bottom: 45%"></div>
        </section>
        <section class="has-background-dark" style="height: 8%; bottom: 0; padding: 1%; padding-top: 0.7%">
            <MessageInput/>
        </section>
    </section>
</template>

<script>
import MessageInput from './subcomp/MessageInput.vue'

export default {
  name: 'Chatroom',
  components: {
      MessageInput
  }
}
</script>