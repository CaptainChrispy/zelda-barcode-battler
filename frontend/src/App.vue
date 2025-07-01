<script setup lang="ts">
import { useI18n } from 'vue-i18n'
import HelloWorld from './components/HelloWorld.vue'

const { t, locale } = useI18n()
</script>

<template>
  <div>
    <a href="https://vite.dev" target="_blank">
      <img src="/vite.svg" class="logo" alt="Vite logo" />
    </a>
    <a href="https://vuejs.org/" target="_blank">
      <img src="./assets/vue.svg" class="logo vue" alt="Vue logo" />
    </a>
    <div style="margin-top: 1em;">
      <button @click="locale = 'en'">English</button>
      <button @click="locale = 'ja'">日本語</button>
    </div>
    <p>{{ t('message.hello') }}</p>
  </div>
  <HelloWorld msg="Vite + Vue" />
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
