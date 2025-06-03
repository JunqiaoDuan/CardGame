<script lang="ts">
import { defineComponent } from 'vue'
import CardTable from '../components/CardTable.vue'
import HomeHeader from '../components/HomeHeader.vue'

export default defineComponent({
  name: 'HomeView',
  components: {
    CardTable,
    HomeHeader
  }
})
</script>

<template>
  <main>
    <HomeHeader />
    <CardTable />
  </main>
</template>

