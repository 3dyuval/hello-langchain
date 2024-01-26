<script setup lang="ts">
import HelloLangchain from './components/HelloLangchain.vue'
</script>

<template>
  <div>
    <a href="https://js.langchain.com/docs/get_started/quickstart" target="_blank" style="font-size: 4rem;">
      🦜
    </a>
  </div>
  <suspense>
  <template #fallback>
    <div class="card">
      <h1>Loading...</h1>
    </div>
  </template>
    <template #default>
      <HelloLangchain msg="Hello, [...] // what word should come next?" />
    </template>
  </suspense>
</template>

<style scoped>

</style>
