<template>
  <div id="app">
    <div style="height: 10rem; background-color: rgb(165, 160, 143);"></div>
    <div style="height: 100%" class="has-background-grey-lighter"></div>
    <div>
      <span style="position: absolute; bottom: 0.5%; left: 50%; transform: translate(-50%, 0);">
        <small><sub>© 2005-2020 Kuycompany. All rights reserved.</sub></small>
      </span>
    </div>
    <div style="position: absolute; top: 5%; left: 5%; width: 90%; height: 90%">
      <div class="box" style="height: 100%; padding: 0; box-shadow: 0 0 15px 0.2px lightgoldenrodyellow">
        <div class="columns" style="height: 100%; padding: 0; margin: 0">
          <Roomlist class="column is-one-fifth has-background-dark" style="padding: 0;"/>
          <Chatroom class="column has-background-grey" style="padding: 0"/>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import Chatroom from './components/Chatroom.vue'
  import Roomlist from './components/Roomlist.vue'

  export default {
    name: 'App',
    components: {
      Chatroom,
      Roomlist
    }
  }
</script>