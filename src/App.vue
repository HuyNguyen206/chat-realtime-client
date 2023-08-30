<script setup>
import HelloWorld from './components/HelloWorld.vue'
import TheWelcome from './components/TheWelcome.vue'
import {io} from 'socket.io-client'
import {onBeforeMount, ref} from "vue";

const socket = io('http://localhost:3001')
const messages = ref([])

onBeforeMount(() => {
    socket.emit('findAllMessages', {}, (res) => {
        messages.value = res
    })

    socket.on('new message', {}, (res) => {
        messages.value.push(res)
    })
})

const sendMessage = (res) => {
    socket.emit('createMessage', {text: messageText.value}, res =>{
        messages.value.push(res)
    })
}
</script>

<template>
<div class="chat">
    <div class="chat-container">
        <div class="" v-for="message in messages">
            [{{message.name}}]: {{message.text}}
        </div>
    </div>
</div>
</template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
