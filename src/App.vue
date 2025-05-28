<template>
  <div id="app">
  <Header 
    :mineCount="mineCount" 
    :timer="timer" 
    :flagCount="flagCount" 
    @changeDifficulty="changeDifficulty"
    @restart="restartGame" 
  />
  <Board 
      :difficulty="difficulty"
      :key="boardKey"
      @update:flagCount="updateFlagCount" 
      :onFirstClick="startTimer"
      :onStopTimer="stopTimer"
  />
  </div>
</template>

<script setup>
import { ref, onUnmounted, watch } from 'vue';
import Header from './components/Header.vue';
import Board from './components/Board.vue';

const difficulty = ref('mudah');
const mineCount = ref(10);
const timer = ref(0);
const flagCount = ref(0);
const boardKey = ref(Date.now());
let interval = null;

function changeDifficulty(level) {
  difficulty.value = level;

  if (level === 'sedang') {
    mineCount.value = 40;
  } else if (level === 'sulit') {
    mineCount.value = 99;
  } else {
    mineCount.value = 10;
  }
  flagCount.value = 0;
  stopTimer();
  timer.value = 0;
  restartGame();
}

function startTimer() {
  if (interval !== null) return;
  interval = setInterval(() => {
    timer.value++;
  }, 1000);
}

function stopTimer() {
  clearInterval(interval);
  interval = null;
}

function updateFlagCount(count) {
  flagCount.value = count;
}

function restartGame() {
  stopTimer();
  timer.value = 0;
  flagCount.value = 0;
  boardKey.value = Date.now(); 
}

onUnmounted(() => stopTimer());
</script>