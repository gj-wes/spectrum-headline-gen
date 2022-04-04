<template>
  <Header />
  <main class="stv-grid">
    <STVItem v-for="(item, ind) of sundayTVItems" :filename="item" :id="uuid" :key="ind"/>
  </main>
</template>

<script>
import Header from './components/Header.vue'
import STVItem from './components/STVItem.vue'

import { v4 as uuidv4 } from 'uuid';

export default {
  name: 'App',
  components: {
    Header,
    STVItem
  },
  data() {
    return {
      sundayTVItems: [
        'pod1-header--dt',
        'pod1-header--mb',
        'pod2-header--dt',
        'pod2-header--mb',
        'pod3-header--dt',
        'pod3-header--mb',
      ]
    }
  },
  computed: {
    uuid() {
      return uuidv4()
    }
  }
}
</script>

<style>
.stv-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, 340px);
  gap: 2rem;
  place-content: center;
  padding: 2rem;
}
</style>
