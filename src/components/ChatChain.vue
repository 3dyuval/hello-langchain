<script setup lang="ts">
import { ChatOpenAI } from "@langchain/openai";
import {ref} from "vue";
import {PromptTemplate} from "@langchain/core/prompts";

const chat = ref([])


const joke = {
  name: 'joke',
  description: 'A joke',
  parameters: {
    type: 'object',
    properties: {
      setup: {
        type: 'string',
        description: 'The setup of the joke'
      },
      punchline: {
        type: 'string',
        description: 'The punchline of the joke'
      }
    }
  },
  required: ["setup", "punchline"],
};

const chatModel = new ChatOpenAI({
  openAIApiKey: import.meta.env['APP_OPENAI_API_KEY']
}).bind({
  functions: [joke],
  function_call: { name: 'joke' }
})


const promptTempate = PromptTemplate.fromTemplate("Tell me a joke about {topic}")

const chain = promptTempate.pipe(chatModel)

const chatCard = ref()

chat.value = await new Promise((resolve) => {
  setTimeout(() => {
    resolve([
      {text: 'Hello, I am Langchain 🦜', type: 'bot'},
      {text: 'Give me a TOPIC', type: 'bot'}
    ])
  }, 1000)
});

function scrollToBottom() {
  chatCard.value.scrollTop = chatCard.value.scrollHeight;
}
async function handleUserMessage({target}: FormDataEvent) {
  const topic = target.message.value
  chat.value.push({text: topic, type: 'user'})
  scrollToBottom()
  const response = await chain.invoke({ topic }).then((kwargs) => {
    target.message.value = ''
    return JSON.parse(kwargs.additional_kwargs.function_call.arguments)
  })
  chat.value.push({text: response, type: 'bot'})
  scrollToBottom()
}
</script>

<template>
  <div class="chat-card" ref="chatCard">
      <transition-group name="chat" tag="ul">
        <template v-for="msg in chat">
          <li v-if="msg.type === 'user'" class="user">{{ msg.text }}</li>
          <template v-else>
              <li :class="['bot', 'setup']">{{ msg.text.setup }}</li>
              <li :class="['bot', 'punchline']">{{ msg.text.punchline }}</li>
          </template>
      </template>
      </transition-group>
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

.chat-enter-active,
.chat-leave-active {
  transition: all 0.5s ease;
}
.chat-enter-from,
.chat-leave-to {
  opacity: 0;
}

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

      &.setup {
        font-weight: normal;
      }

      &.punchline {
        font-weight: bold;
        transition-delay: 1s;
      }
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