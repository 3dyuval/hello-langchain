<script setup lang="ts">
import { OpenAI  } from "@langchain/openai";
import {ref} from "vue";

const chat = ref([])
const chatModel = new OpenAI({
  openAIApiKey: import.meta.env['APP_OPENAI_API_KEY']
})

const chatCard = ref()

chat.value = await new Promise((resolve) => {
  setTimeout(() => {
    resolve([
      {text: 'Hello, I am Langchain 🦜', type: 'bot'},
      {text: 'How can I help you?', type: 'bot'}
    ])
  }, 1000)
});

function scrollToBottom() {
  chatCard.value.scrollTop = chatCard.value.scrollHeight;
}
async function handleUserMessage({target}: FormDataEvent) {
  const userMessage = target.message.value
  chat.value.push({text: userMessage, type: 'user'})
  scrollToBottom()
  const response = await chatModel.invoke(userMessage).then((res) => {
    target.message.value = ''
    return res
  })
  chat.value.push({text: response, type: 'bot'})
  scrollToBottom()
}
</script>

<template>
  <div class="chat-card" ref="chatCard">
    <ul>
    <li v-for="msg in chat" :key="msg.id" :class="msg.type">{{ msg.text }}</li>
    </ul>
    <form
        autocomplete="off"
        @submit.prevent="handleUserMessage">
      <fieldset class="user-input">
      <input name="message" class="chat-user-input" type="text" size="100"
    placeholder="Type your message here..."
    />
      <button type="submit">Send</button>
      </fieldset>
    </form>
  </div>
</template>

<style lang="scss">

.chat-card {
  max-height: 50vh;
  overflow: auto;
  margin: 0 auto;
  max-width: min(500px, 50%);
  padding: 1rem;
  border-radius: 1rem;
  &:focus-within {
    background: #1c1c1c;
  }
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

fieldset.user-input {
  border: none;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  gap: 1rem;
  button  {
    padding: 1rem;
  }
}
.chat-user-input {
  all: unset;
  outline: 1px solid rgba(204, 204, 204, 0.25);
  padding: 10px;
  font-size: 16px;
  border-radius: 5px;
  width: 100%;
  box-sizing: border-box;
  height: 50px;
  text-align: left;
  &:focus {
    outline: 1px solid #ccc;
  }
}
</style>