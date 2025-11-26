<script setup lang="ts">
import { ref, onMounted } from 'vue'
import MyTasks from './components/MyList.vue'
import LoginForm from './components/LoginForm.vue'
import logo from '@/assets/todolisteLogo.png'

// Login-Status
const isLoggedIn = ref(false)
const token = ref<string | null>(null)
const username = ref<string | null>(null)

// Beim Laden prüfen, ob schon ein Token gespeichert ist
onMounted(() => {
  const savedToken = localStorage.getItem('authToken')
  const savedUser = localStorage.getItem('username')

  if (savedToken) {
    token.value = savedToken
    username.value = savedUser
    isLoggedIn.value = true
  }
})

// Wird von <LoginForm> aufgerufen, wenn Login geklappt hat
const handleLoginSuccess = (payload: { token: string; username: string }) => {
  token.value = payload.token
  username.value = payload.username
  isLoggedIn.value = true
}

// Logout
const logout = () => {
  localStorage.removeItem('authToken')
  localStorage.removeItem('username')
  token.value = null
  username.value = null
  isLoggedIn.value = false
}
</script>

<template>
  <main>
    <!-- Logo -->
    <img :src="logo" alt="Mein Logo" class="logo" />
    <h1>Meine Aufgabenübersicht</h1>

    <!-- Wenn NICHT eingeloggt: Login-Formular -->
    <LoginForm
      v-if="!isLoggedIn"
      @login-success="handleLoginSuccess"
    />

    <!-- Wenn eingeloggt: Begrüßung + Logout + Aufgabenliste -->
    <div
      v-else
      class="container mt-3"
    >
      <div class="d-flex justify-content-between align-items-center mb-3">
        <p class="mb-0">
          Eingeloggt als
          <strong>{{ username ?? 'User' }}</strong>
        </p>
        <button
          class="btn btn-outline-secondary btn-sm"
          @click="logout"
        >
          Logout
        </button>
      </div>

      <!-- To-Do-Liste bekommt den Token (optional im Backend auswertbar) -->
      <MyTasks :token="token" />
    </div>
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
  color: var(--color-heading);
  margin-bottom: 24px;
}

/* Logo-Styling */
.logo {
  width: 120px;
  height: auto;
  margin-bottom: 16px;
}
</style>
