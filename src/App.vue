<script setup>
import HelloWorld from './components/HelloWorld.vue'
import TheWelcome from './components/TheWelcome.vue'
import {io} from 'socket.io-client'
import {onBeforeMount, ref} from "vue";

const socket = io('http://localhost:3001')
const messages = ref([])
const messageText = ref(null)
const isJoined = ref(false)
const name = ref(null)
const isTyping = ref(false)
const typingPerson = ref(null)

onBeforeMount(() => {
    socket.emit('findAllMessages', {}, (res) => {
        messages.value = res
    })

    socket.on('new message', (res) => {
        messages.value.push(res)
    })

  socket.on('typing', (res) => {
    isTyping.value = res.isTyping
    typingPerson.value = res.name
  })
})

const typing = () => {
  socket.emit('typing', {isTyping: true})
  setTimeout(() => {
    socket.emit('typing', {isTyping: false})
  }, 2000)
}

const join = () => {
  socket.emit('join', {name: name.value}, (res) => {
    isJoined.value = true
  })
}

const sendMessage = () => {
    socket.emit('createMessage', {name:name.value, text:messageText.value}, res => {
      messageText.value = null
    })
}
</script>

<template>
<div class="chat">
  <div class="" v-if="!isJoined">
    <form action="" @submit.prevent="join()">
      <label for="">Name</label>
      <input type="text" name="" id="" v-model="name">
      <button type="submit">Join</button>
    </form>
  </div>

    <div v-else class="chat-container">
      <div v-if="isTyping">{{typingPerson}} is typing...</div>

      <form @submit.prevent="sendMessage()" action="">
        <label for="">New message</label>
        <input type="text" name="" id="" v-model="messageText" @input="typing">
        <button type="submit">Send</button>

      </form>
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
