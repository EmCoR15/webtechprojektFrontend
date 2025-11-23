<script setup lang="ts">
import { ref, onMounted } from 'vue'

// Typ passend zu deinem Java-Backend
type ToDoEntry = {
  id: number;
  name: string;
  description: string;
  dueTime: string; // kommt als ISO-String vom Backend
  done: boolean;
}

// Reaktive Liste
const tasks = ref<ToDoEntry[]>([])
const error = ref<string | null>(null)

// Load-Funktion
const loadTodos = async () => {
  try {
    const base = import.meta.env.VITE_BACKEND_URL
    const res = await fetch(`${base}/todos`)

    if (!res.ok) {
      throw new Error(`Backend error: ${res.status}`)
    }

    tasks.value = await res.json()

  } catch (e) {
    error.value = (e as Error).message ?? "Unbekannter Fehler"
    console.error("FEHLER beim Laden:", e)
  }

}

// Wird automatisch ausgeführt, wenn die Komponente geladen wird
onMounted(() => {
  loadTodos()
})
</script>

<template>
  <section class="card">
    <h2>Tasks für heute</h2>

    <!-- Fehler anzeigen -->
    <p v-if="error" style="color:red">{{ error }}</p>

    <ul v-else>
      <li
        v-for="(task, i) in tasks"
        :key="task.id"
        class="task"
      >
        <span class="index">{{ i + 1 }}.</span>
        <span>
          <b>{{ task.name }}</b><br>
          <small>{{ task.description }}</small>
        </span>
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
