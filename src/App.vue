<template>
  <Header />
  <main class="stv-grid">
    <STVItem v-for="(item, ind) of sundayTVItems" :filename="item" :id="ind" :key="ind"/>
  </main>
  <button class="download-all" @click.prevent="downloadAll">Download all</button>
</template>

<script>
import Header from './components/Header.vue'
import STVItem from './components/STVItem.vue'
import downloadAll from './util/downloadAll'

export default {
  name: 'App',
  components: {
    Header,
    STVItem
  },
  data() {
    return {
      sundayTVItems: [
        'grid-header--dt',
        'grid-header--mb',
        'pod1-header--dt',
        'pod1-header--mb',
        'pod2-header--dt',
        'pod2-header--mb',
        'pod3-header--dt',
        'pod3-header--mb',
        'pod4-header--dt',
        'pod4-header--mb',
        'pod5-header--dt',
        'pod5-header--mb',
        'pod6-header--dt',
        'pod6-header--mb',
        'pod7-header--dt',
        'pod7-header--mb',
        'pod8-header--dt',
        'pod8-header--mb',
      ]
    }
  },
  methods: {
    downloadAll() {
      const downloadableLinks = [...document.querySelectorAll('a[hascopy="true"]')]
      downloadAll(downloadableLinks)
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
.download-all {
  position: fixed;
  bottom: 2rem;
  right: 2rem;
  font-size: 2rem;
  font-family: inherit;
  border-radius: 5px;
  border: 1px solid hsl(0, 0%, 13%);
}
.dark .download-all {
  background-color: hsl(0, 0%, 12%);
  color: hsl(0, 0%, 87%);
  border: 1px solid hsl(0, 0%, 87%);
}
</style>
