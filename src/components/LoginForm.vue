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
  <div class="login-shell">
    <div class="matrix-card">
      <div class="matrix-glow"></div>
      <div class="matrix-grid"></div>

      <header class="matrix-header">
        <div class="pill">system.login</div>
        <h2>Enter the Matrix</h2>
        <p>Authentifizieren, um das Aufgaben-Netzwerk zu betreten.</p>
      </header>

      <form
        class="matrix-form"
        @submit.prevent="handleLogin"
      >
        <label class="input-label" for="username">Benutzername</label>
        <div class="input-shell">
          <span aria-hidden="true">></span>
          <input
            id="username"
            v-model="username"
            type="text"
            autocomplete="username"
            required
            placeholder="neo"
          />
        </div>

        <label class="input-label" for="password">Passwort</label>
        <div class="input-shell">
          <span aria-hidden="true">></span>
          <input
            id="password"
            v-model="password"
            type="password"
            autocomplete="current-password"
            required
            placeholder="••••••••"
          />
        </div>

        <button
          type="submit"
          class="matrix-button"
          :disabled="loading"
        >
          {{ loading ? 'Verbindung wird aufgebaut...' : 'Login initiieren' }}
        </button>

        <p v-if="error" class="error-text">
          {{ error }}
        </p>
      </form>
    </div>
  </div>
</template>

<style scoped>
:global(body) {
  background-color: #030b07;
}

.login-shell {
  width: min(480px, 92vw);
  margin: 24px auto;
}

.matrix-card {
  position: relative;
  padding: 32px 28px;
  border-radius: 18px;
  background: rgba(5, 15, 10, 0.92);
  overflow: hidden;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.6), 0 0 30px rgba(0, 255, 140, 0.2);
  border: 1px solid rgba(0, 255, 140, 0.35);
  backdrop-filter: blur(4px);
}

.matrix-glow {
  position: absolute;
  inset: -40%;
  background: radial-gradient(circle at 50% 40%, rgba(0, 255, 140, 0.12), transparent 40%);
  filter: blur(32px);
  opacity: 0.75;
  pointer-events: none;
}

.matrix-grid {
  position: absolute;
  inset: -2px;
  background-image: linear-gradient(rgba(0, 255, 140, 0.08) 1px, transparent 1px),
    linear-gradient(90deg, rgba(0, 255, 140, 0.08) 1px, transparent 1px);
  background-size: 26px 26px, 26px 26px;
  opacity: 0.25;
  mask-image: radial-gradient(circle at 20% 20%, rgba(0, 0, 0, 0.7), transparent 55%);
  pointer-events: none;
}

.matrix-header {
  position: relative;
  text-align: left;
  color: #eafff1;
  margin-bottom: 24px;
  font-family: 'Share Tech Mono', 'Roboto Mono', monospace;
  letter-spacing: 0.4px;
}

.matrix-header h2 {
  margin: 8px 0 6px;
  font-size: 24px;
  text-transform: uppercase;
  color: #7df7b1;
}

.matrix-header p {
  margin: 0;
  color: rgba(234, 255, 241, 0.75);
  line-height: 1.5;
}

.pill {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 6px 10px;
  font-size: 12px;
  letter-spacing: 1px;
  background: rgba(0, 255, 140, 0.1);
  border: 1px solid rgba(0, 255, 140, 0.6);
  border-radius: 999px;
  box-shadow: 0 0 8px rgba(0, 255, 140, 0.35);
}

.matrix-form {
  position: relative;
  display: flex;
  flex-direction: column;
  gap: 12px;
  z-index: 1;
}

.input-label {
  font-size: 13px;
  color: rgba(234, 255, 241, 0.72);
  font-family: 'Share Tech Mono', 'Roboto Mono', monospace;
}

.input-shell {
  display: grid;
  grid-template-columns: auto 1fr;
  align-items: center;
  gap: 10px;
  padding: 10px 14px;
  border-radius: 12px;
  background: linear-gradient(145deg, rgba(7, 20, 14, 0.85), rgba(4, 12, 9, 0.95));
  border: 1px solid rgba(0, 255, 140, 0.35);
  box-shadow: inset 0 0 0 1px rgba(0, 255, 140, 0.08), 0 0 0 1px rgba(0, 0, 0, 0.6);
  transition: border-color 0.2s ease, box-shadow 0.2s ease, transform 0.2s ease;
}

.input-shell span {
  color: #00ff8c;
  font-weight: 700;
  letter-spacing: 0.08em;
}

.input-shell input {
  background: transparent;
  border: none;
  outline: none;
  color: #eafff1;
  font-size: 16px;
  font-family: 'Share Tech Mono', 'Roboto Mono', monospace;
  letter-spacing: 0.5px;
}

.input-shell:focus-within {
  border-color: rgba(0, 255, 140, 0.7);
  box-shadow: 0 0 12px rgba(0, 255, 140, 0.3);
  transform: translateY(-1px);
}

.matrix-button {
  margin-top: 10px;
  padding: 12px 14px;
  background: linear-gradient(135deg, rgba(0, 255, 140, 0.32), rgba(0, 196, 108, 0.4));
  border: 1px solid rgba(0, 255, 140, 0.8);
  color: #042312;
  font-weight: 700;
  font-size: 15px;
  text-transform: uppercase;
  letter-spacing: 1px;
  border-radius: 12px;
  cursor: pointer;
  transition: transform 0.2s ease, box-shadow 0.2s ease, filter 0.2s ease;
  box-shadow: 0 14px 30px rgba(0, 255, 140, 0.25), inset 0 0 0 1px rgba(0, 0, 0, 0.4);
}

.matrix-button:hover:not(:disabled) {
  transform: translateY(-2px) scale(1.01);
  filter: brightness(1.05);
  box-shadow: 0 18px 40px rgba(0, 255, 140, 0.32), inset 0 0 0 1px rgba(0, 0, 0, 0.35);
}

.matrix-button:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.error-text {
  margin: 10px 0 0;
  color: #ff7a7a;
  font-family: 'Share Tech Mono', 'Roboto Mono', monospace;
  text-align: left;
  background: rgba(255, 122, 122, 0.06);
  border: 1px solid rgba(255, 122, 122, 0.2);
  padding: 10px 12px;
  border-radius: 10px;
}

@media (max-width: 480px) {
  .matrix-card {
    padding: 26px 22px;
  }

  .matrix-header h2 {
    font-size: 21px;
  }
}
</style>
