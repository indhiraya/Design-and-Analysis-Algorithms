<template>
  <div class="header">
    <select class="difficulty" v-model="selectedDifficulty" @change="emitDifficulty">
      <option value="mudah">Easy</option>
      <option value="sedang">Intermediate</option>
      <option value="sulit">Advanced</option>
    </select>
    <div class="info">
      <span class="icon">🏠</span> {{ (mineCount || 0) - (flagCount || 0) }}
      <span class="icon">⏱️</span> {{ timer }}
    </div>
    <div class="actions">
      <button class="icon" @click="emitRestart">🔄</button>
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue';

const props = defineProps({
  mineCount: Number,
  timer: Number,
  flagCount: Number
});

const emit = defineEmits(['changeDifficulty', 'restart']);
const selectedDifficulty = ref('mudah');

function emitDifficulty() {
  emit('changeDifficulty', selectedDifficulty.value);
}

function emitRestart() {
  emit('restart');
}
</script>