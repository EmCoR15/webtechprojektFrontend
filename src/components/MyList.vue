<script setup lang="ts">
import { ref, onMounted } from 'vue'

type ToDoEntry = {
  id: number;
  name: string;
  description: string;
  dueTime: string;
  done: boolean;
}

// 👉 Token als Prop entgegennehmen (kann null sein)
const props = defineProps<{
  token?: string | null
}>()

// Reaktive Liste
const tasks = ref<ToDoEntry[]>([])
const error = ref<string | null>(null)

// Load-Funktion
const loadTodos = async () => {
  try {
    const base = import.meta.env.VITE_BACKEND_URL

    // 👉 Header vorbereiten
    const headers: HeadersInit = {}
    if (props.token) {
      headers['Authorization'] = `Bearer ${props.token}`
    }

    const res = await fetch(`${base}/todos`, { headers })

    if (!res.ok) {
      throw new Error(`Backend error: ${res.status}`)
    }

    tasks.value = await res.json()
  } catch (e) {
    error.value = (e as Error).message ?? 'Unbekannter Fehler'
    console.error('FEHLER beim Laden:', e)
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
    <p v-if="error" class="text-danger mb-0">{{ error }}</p>

    <!-- Tabelle statt UL-Liste -->
    <div v-else class="table-responsive">
      <table class="table table-striped table-hover align-middle mb-0">
        <thead class="table-light">
        <tr>
          <th scope="col" style="width: 3rem;">#</th>
          <th scope="col">Name</th>
          <th scope="col">Beschreibung</th>
          <th scope="col" style="width: 8rem;">Fällig</th>
          <th scope="col" style="width: 7rem;">Status</th>
        </tr>
        </thead>

        <tbody>
        <tr
          v-for="(task, i) in tasks"
          :key="task.id"
        >
          <th scope="row">{{ i + 1 }}</th>
          <td>{{ task.name }}</td>
          <td class="text-muted">
            {{ task.description }}
          </td>
          <td>{{ task.dueTime || '-' }}</td>
          <td>
              <span
                class="badge"
                :class="task.done ? 'bg-success' : 'bg-secondary'"
              >
                {{ task.done ? 'Erledigt' : 'Offen' }}
              </span>
          </td>
        </tr>

        <tr v-if="tasks.length === 0">
          <td colspan="5" class="text-center text-muted">
            Keine Tasks vorhanden.
          </td>
        </tr>
        </tbody>
      </table>
    </div>
  </section>
</template>

<style scoped>
.card {
  background: #fff;
  border: 1px solid #e5e7eb;
  border-radius: 16px;
  padding: 16px;
  max-width: 800px;
  box-shadow: 0 6px 18px rgba(0,0,0,0.05);
  margin: 1rem auto;
}

h2 {
  margin: 0 0 16px;
  font-size: 1.25rem;
  color: #333;
}
</style>
