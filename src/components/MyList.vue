<script setup lang="ts">
import { ref, onMounted } from 'vue'

type ToDoEntry = {
  id: number
  name: string
  description: string
  dueTime: string
  done: boolean
}

// Token kommt optional von App.vue
const props = defineProps<{
  token?: string | null
}>()

const tasks = ref<ToDoEntry[]>([])
const error = ref<string | null>(null)

// Form-Felder für neues Todo
const newName = ref('')
const newDescription = ref('')
const newDueTime = ref('') // wird aus <input type="datetime-local"> kommen

const loadTodos = async () => {
  try {
    const base = import.meta.env.VITE_BACKEND_URL

    const headers: Record<string, string> = {}
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

// POST: neues Todo speichern
const addTodo = async () => {
  try {
    const base = import.meta.env.VITE_BACKEND_URL

    const headers: Record<string, string> = {
      'Content-Type': 'application/json',
    }
    if (props.token) {
      headers['Authorization'] = `Bearer ${props.token}`
    }

    const body = {
      name: newName.value,
      description: newDescription.value,
      dueTime: newDueTime.value || null,
      done: false,
    }

    const res = await fetch(`${base}/todos`, {
      method: 'POST',
      headers,
      body: JSON.stringify(body),
    })

    if (!res.ok) {
      throw new Error(`Fehler beim Speichern: ${res.status}`)
    }

    const saved: ToDoEntry = await res.json()

    // direkt in die Liste übernehmen
    tasks.value.push(saved)

    // Formular leeren
    newName.value = ''
    newDescription.value = ''
    newDueTime.value = ''
  } catch (e) {
    error.value = (e as Error).message ?? 'Unbekannter Fehler beim Speichern'
    console.error('FEHLER beim Speichern:', e)
  }
}
//Löschen-Funktion
const deleteTodo = async (id: number) => {
  if (!confirm('Task wirklich löschen?')) {
    return
  }

  try {
    const base = import.meta.env.VITE_BACKEND_URL

    const headers: Record<string, string> = {}
    if (props.token) {
      headers['Authorization'] = `Bearer ${props.token}`
    }

    const res = await fetch(`${base}/todos/${id}`, {
      method: 'DELETE',
      headers,
    })

    if (!res.ok) {
      throw new Error(`Fehler beim Löschen: ${res.status}`)
    }

    // Aus der Liste entfernen
    tasks.value = tasks.value.filter((t) => t.id !== id)
  } catch (e) {
    error.value = (e as Error).message ?? 'Unbekannter Fehler beim Löschen'
    console.error('FEHLER beim Löschen:', e)
  }
}

onMounted(() => {
  loadTodos()
})
</script>

<template>
  <section class="card">
    <h2>Tasks für heute</h2>

    <p v-if="error" class="text-danger mb-0">{{ error }}</p>

    <form class="row g-2 mb-3 text-start" @submit.prevent="addTodo">
      <div class="col-12 col-md-3">
        <label class="form-label small mb-1">Name</label>
        <input
          v-model="newName"
          type="text"
          class="form-control form-control-sm"
          placeholder="Titel eingeben"
          required
        />
      </div>

      <div class="col-12 col-md-3">
        <label class="form-label small mb-1">Beschreibung</label>
        <input
          v-model="newDescription"
          type="text"
          class="form-control form-control-sm"
          placeholder="Optional"
        />
      </div>

      <div class="col-12 col-md-3">
        <label class="form-label small mb-1">Fällig bis</label>
        <input v-model="newDueTime" type="datetime-local" class="form-control form-control-sm" />
      </div>

      <div class="col-12 col-md-3 d-flex align-items-end">
        <button type="submit" class="btn btn-success btn-sm w-100">Speichern</button>
      </div>
    </form>

    <div class="table-responsive">
      <table class="table table-striped table-hover align-middle mb-0">
        <thead class="table-light">
          <tr>
            <th scope="col" style="width: 3rem">#</th>
            <th scope="col">Name</th>
            <th scope="col">Beschreibung</th>
            <th scope="col" style="width: 8rem">Fällig</th>
            <th scope="col" style="width: 7rem">Status</th>
            <th scope="col" style="width: 5rem">Aktion</th>
            <!-- NEU -->
          </tr>
        </thead>

        <tbody>
          <tr v-for="(task, i) in tasks" :key="task.id">
            <th scope="row">{{ i + 1 }}</th>
            <td>{{ task.name }}</td>
            <td class="text-muted">{{ task.description }}</td>
            <td>{{ task.dueTime || '-' }}</td>
            <td>
              <span class="badge" :class="task.done ? 'bg-success' : 'bg-secondary'">
                {{ task.done ? 'Erledigt' : 'Offen' }}
              </span>
            </td>
            <td>
              <!-- NEU -->
              <button @click="deleteTodo(task.id)" class="btn btn-danger btn-sm" title="Löschen">
                🗑️
              </button>
            </td>
          </tr>

          <tr v-if="tasks.length === 0">
            <td colspan="6" class="text-center text-muted">Keine Tasks vorhanden.</td>
            <!-- colspan 5 -> 6 -->
          </tr>
        </tbody>
      </table>
    </div>
  </section>
</template>

<style scoped>
.card {
  background: var(--color-background-soft);
  border: 1px solid var(--color-border);
  border-radius: 16px;
  padding: 16px;
  max-width: 800px;
  box-shadow: 0 6px 18px rgba(0, 0, 0, 0.05);
  margin: 1rem auto;
}

h2 {
  margin: 0 0 16px;
  font-size: 1.25rem;
  color: var(--color-heading);
}
.form-control::placeholder {
  color: var(--color-text-muted) !important;
  opacity: 0.6;
}

.form-label {
  color: var(--color-text) !important;
}

/* Tabellen-Styles */
.table {
  color: var(--color-text);
  border-color: var(--color-border);
}

.table > :not(caption) > * > * {
  border-bottom-color: var(--color-border);
  color: var(--color-text);
}

.table-light {
  --bs-table-bg: var(--color-background-mute);
  --bs-table-color: var(--color-text);
  background-color: var(--color-background-mute);
  border-color: var(--color-border);
}

.table-light th {
  color: var(--color-text) !important;
  border-color: var(--color-border) !important;
}

.table-striped > tbody > tr:nth-of-type(odd) > * {
  --bs-table-bg-type: var(--color-background-mute);
  background-color: var(--color-background-mute);
  color: var(--color-text);
}

.table-striped > tbody > tr:nth-of-type(even) > * {
  background-color: transparent;
  color: var(--color-text);
}

td,
th {
  color: var(--color-text) !important;
  border-color: var(--color-border) !important;
}

.text-muted {
  color: var(--color-text-muted) !important;
  opacity: 0.7;
}

.badge {
  /* Badges behalten ihre Bootstrap-Farben */
}
</style>
