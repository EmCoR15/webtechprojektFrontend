<script setup lang="ts">
import MyTasks from './components/MyList.vue'
</script>

<template>
  <main>
    <h1>Meine Aufgabenübersicht</h1>
    <MyTasks />
  </main>
</template>

<style scoped>
main {
  text-align: center;
  padding: 40px 16px;
  font-family: system-ui, sans-serif;
  background: radial-gradient(circle at 50% -20%, rgba(34,197,94,.08), transparent 50%);
  min-height: 100vh;
}
h1 {
  color: #e5e7eb;
  margin-bottom: 24px;
}
</style>
