<template>
    <div class="field has-addons">
        <div class="control is-expanded">
            <input class="input is-medium" type="text" placeholder="Type a Message...">
        </div>
        <div class="control">
            <a class="button is-medium is-success">
                <span class="icon">
                    <i class="fas fa-paper-plane"></i>
                </span>
                <span>Send</span>
            </a>
        </div>
    </div>
</template>

<script>
export default {
  name: 'MessageInput',
}
</script>