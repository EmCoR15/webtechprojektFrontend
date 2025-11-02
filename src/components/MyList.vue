<script setup lang="ts">
import { ref } from 'vue'

// Reaktive Liste von Aufgaben (string[])
const tasks = ref<string[]>([
  'Projektidee besprechen',
  'Vue-Komponente fertigstellen',
  'Code auf GitHub pushen'
])
</script>

<template>
  <section class="card">
    <h2>Tasks für heute</h2>
    <ul>
      <li
        v-for="(task, i) in tasks"
        :key="i"
        class="task"
      >
        <span class="index">{{ i + 1 }}.</span>
        <span>{{ task }}</span>
      </li>
    </ul>
  </section>
</template>

<style scoped>
.card {
  background: #fff;
  border: 1px solid #e5e7eb;
  border-radius: 16px;
  padding: 16px;
  max-width: 500px;
  box-shadow: 0 6px 18px rgba(0,0,0,0.05);
  margin: 1rem auto;
}

h2 {
  margin: 0 0 12px;
  font-size: 1.25rem;
  color: #333;
}

ul {
  list-style: none;
  padding: 0;
  margin: 0;
  display: grid;
  gap: 10px;
}

.task {
  color: #181818;
  display: flex;
  align-items: center;
  gap: 10px;
  background: #f9fafb;
  padding: 10px 14px;
  border-radius: 12px;
  transition: background .2s ease;
}

.task:hover {
  background: #edf6ee;
}

.index {
  color: #10b981;
  font-weight: 600;
  width: 24px;
  text-align: right;
}
</style>
