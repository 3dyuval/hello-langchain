<script setup lang="ts">
import { OpenAI  } from "@langchain/openai";
import {ref} from "vue";

const chat = ref([])
const chatModel = new OpenAI({
  openAIApiKey: import.meta.env['APP_OPENAI_API_KEY']
})


chat.value = await new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve([{text: 'hello. I am a chat bot'}, {text: 'How can I help?'}])
  }, 1000)
});

async function handleUserMessage({target}: FormDataEvent) {
  const userMessage = target.message.value
  chat.value.push({text: userMessage})

  const response = await chatModel.invoke(userMessage).then((res) => {
    target.message.value = ''
    return res
  })
  chat.value.push({text: response})
}
</script>

<template>
  <div class="chat-card">
    <li v-for="msg in chat" :key="msg.id">{{ msg.text }}</li>
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