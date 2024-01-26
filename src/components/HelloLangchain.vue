<script setup lang="ts">
import { OpenAI  } from "@langchain/openai";

const chatModel = new OpenAI({
  openAIApiKey: import.meta.env['APP_OPENAI_API_KEY']
})


const langchain = await chatModel.invoke('Hello, [...] // fill in the blank with a greeting')

</script>

<template>
  <div class="card">
    <h1>Hello, {{ langchain }}</h1>
    <p>
      <code>components/HelloLangchain.vue</code>
    </p>
  </div>

</template>
