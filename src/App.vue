<script setup lang="ts">
import HelloLangchain from './components/HelloLangchain.vue'
</script>

<template>
  <div>
    <a href="https://vuejs.org/" target="_blank" style="font-size: 4rem;">
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
      <HelloLangchain msg="Vite + Vue" />
    </template>
  </suspense>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
