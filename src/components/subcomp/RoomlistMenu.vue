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
                            <a class="button is-success" onclick="new_chatroom()">Yes</a>
                        </p>
                        <p class="control">
                            <a class="button is-danger" onclick="cancel_new()">Cancel</a>
                        </p>
                    </div>
                </div>
                <div id="join_menu" class="box has-background-success my-0 dsbl" style="padding: 3%;">
                    <p>Chat ID</p>
                    <input type="text" class="input">
                    <div class="field is-grouped is-grouped-right">
                        <p class="control">
                            <a class="button is-info" onclick="join_chatroom()">Join</a>
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
}
</script>