<template>
  <Header />
  <main class="stv-grid">
    <GenItem :filename="file1" :id="uuid"/>
    <GenItem :filename="file2" :id="uuid"/>
  </main>
</template>

<script>
import Header from './components/Header.vue'
import GenItem from './components/GenItem.vue'

import { uuid } from 'uuidv4';

export default {
  name: 'App',
  components: {
    Header,
    GenItem
  },
  data() {
    return {
      file1: 'pod1-header--dt',
      file2: 'pod2-header--dt',
    }
  },
  method: {
    uuid() {
      return uuid()
    }
  }
}
</script>

<style>
.stv-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, 320px);
  gap: 2rem;
  padding: 2rem;
}
</style>
