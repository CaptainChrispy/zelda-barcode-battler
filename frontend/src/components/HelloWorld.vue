<script setup lang="ts">
import { ref } from 'vue';
import Card from './Card.vue';
import cards from '../zelda_cards.json';

const language = ref('en');

function toggleLanguage() {
  language.value = language.value === 'en' ? 'jp' : 'en';
}
</script>

<template>
  <div class="language-toggle">
    <button @click="toggleLanguage">
      Switch to {{ language === 'en' ? 'Japanese' : 'English' }}
    </button>
  </div>
  <div class="card-gallery">
    <Card v-for="card in cards" :key="card.id" :card="card" :language="language" />
  </div>
</template>

<style scoped>
.language-toggle {
  margin-bottom: 1rem;
  text-align: center;
}

.card-gallery {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
  justify-content: center;
}
</style>
