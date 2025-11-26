<script setup lang="ts">
import { ref } from 'vue'

const username = ref('')
const password = ref('')
const error = ref<string | null>(null)
const loading = ref(false)

// Emit-Event nach oben, wenn Login klappt
const emit = defineEmits<{
  (e: 'login-success', payload: { token: string; username: string }): void
}>()

const handleLogin = async () => {
  error.value = null
  loading.value = true

  try {
    const base = import.meta.env.VITE_BACKEND_URL
    const res = await fetch(`${base}/auth/login`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        username: username.value,
        password: password.value,
      }),
    })

    if (!res.ok) {
      throw new Error('Benutzername oder Passwort falsch')
    }

    const data = await res.json()
    // data.token erwarten wir vom Backend

    // Token im LocalStorage speichern
    localStorage.setItem('authToken', data.token)
    localStorage.setItem('username', data.username ?? username.value)

    emit('login-success', {
      token: data.token,
      username: data.username ?? username.value,
    })
  } catch (e) {
    error.value = (e as Error).message ?? 'Login fehlgeschlagen'
  } finally {
    loading.value = false
  }
}
</script>

<template>
  <div class="container mt-4" style="max-width: 400px;">
    <h2 class="mb-3 text-center">Login</h2>

    <form @submit.prevent="handleLogin">
      <div class="mb-3">
        <label class="form-label">Benutzername</label>
        <input
          v-model="username"
          type="text"
          class="form-control"
          required
        />
      </div>

      <div class="mb-3">
        <label class="form-label">Passwort</label>
        <input
          v-model="password"
          type="password"
          class="form-control"
          required
        />
      </div>

      <button
        type="submit"
        class="btn btn-primary w-100"
        :disabled="loading"
      >
        {{ loading ? 'Anmelden...' : 'Anmelden' }}
      </button>

      <p v-if="error" class="text-danger mt-3 mb-0">
        {{ error }}
      </p>
    </form>
  </div>
</template>
