<script setup>
import { useDark, useToggle } from '@vueuse/core'
import { computed } from 'vue';

const isDark = useDark()
const toggleDark = useToggle(isDark)

const buttonIcon = computed(() => isDark.value ? '🌙' : '☀️')
</script>

<template>
  <header>
    <h1>Spectrum Headline Generator</h1>

    <div class="toggle-container">
      <button @click="toggleDark()" class="toggle-button">{{ buttonIcon }}</button>
    </div>
  </header>
</template>

<style scoped>
  header {
    padding: 2rem;
    border-bottom: 1px solid currentColor;
    display: grid;
    grid-template: 1 1;
  }
  h1 {
    margin: 0;
    grid-column: 1 / -1;
    grid-row: 1 / -1;
  }
  .toggle-container {
    grid-column: 1 / -1;
    grid-row: 1 / -1;
    justify-self: flex-end;
    align-self: center;
  }
  .toggle-button {
    background: none;
    border: 1px solid hsl(0, 0%, 10%);
    color: hsl(0, 0%, 100%);
    font-size: inherit;
    font-family: inherit;
    border-radius: 5px;
    padding: .1rem .5rem;
  }
  .dark .toggle-button {
    border: 1px solid hsl(0, 0%, 80%);
  }
</style>