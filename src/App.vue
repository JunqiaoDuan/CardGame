<script lang="ts">
import { defineComponent } from 'vue'
import HomeView from './views/HomeView.vue'

export default defineComponent({
  name: 'App',
  components: {
    HomeView,
  },
})
</script>

<template>
  <main>
    <HomeView />
  </main>
</template>

<style scoped></style>
