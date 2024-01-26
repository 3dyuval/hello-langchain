<script setup lang="ts">
import { OpenAI  } from "@langchain/openai";
import {ref} from "vue";

const chat = ref([])
const chatModel = new OpenAI({
  openAIApiKey: import.meta.env['APP_OPENAI_API_KEY']
})


chat.value = await new Promise((resolve) => {
  setTimeout(() => {
    resolve([
      {text: 'Hello, I am Langchain 🦜', type: 'bot'},
      {text: 'How can I help you?', type: 'bot'}
    ])
  }, 1000)
});

async function handleUserMessage({target}: FormDataEvent) {
  const userMessage = target.message.value
  chat.value.push({text: userMessage, type: 'user'})

  const response = await chatModel.invoke(userMessage).then((res) => {
    target.message.value = ''
    return res
  })
  chat.value.push({text: response, type: 'bot'})
}
</script>

<template>
  <div class="chat-card">
    <ul>
    <li v-for="msg in chat" :key="msg.id" :class="msg.type">{{ msg.text }}</li>
    </ul>
    <form @submit.prevent="handleUserMessage">
      <input name="message" class="chat-user-input" type="text" size="100"
    placeholder="Type your message here..."
    />
      <button type="submit">Send</button>
    </form>
  </div>
</template>

<style lang="scss">

.chat-card {
  li {
    list-style-type: none;
    &.user {
      text-align: right;
      padding: .5rem;
      margin: 1rem 0;
      max-width: max-content;
      border-radius: .5rem;
      background-color: #f5f5f5;
      color: #1a1a1a;

    }
    &.bot {
      text-align: left;
    }
  }

  display: flex;
  gap: .25rem;
  flex-direction: column;
}

.chat-user-input {
  all: unset;
  outline: 1px solid rgba(204, 204, 204, 0.25);
  padding: 10px;
  font-size: 16px;
  border-radius: 5px;
  width: 100%;
  box-sizing: border-box;
  margin-bottom: 10px;
  height: 50px;
  text-align: left;
  &:focus {
    outline: 1px solid #ccc;
  }
}
</style>