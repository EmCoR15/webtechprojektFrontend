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
  <main :class="{ 'matrix-bg': !isLoggedIn }">
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
  padding: 40px 480px;
  font-family: system-ui, sans-serif;
  background: radial-gradient(circle at 50% -20%, rgba(34,197,94,.08), transparent 50%);
  min-height: 100vh;
  position: relative;
  overflow: hidden;
}

button {
  color : #f8f8f8;}

p {
  font-size: 14px;
  color: #f8f8f8;
}

h1 {
  color: var(--bs-green);
  margin-bottom: 24px;
}

/* Logo-Styling */
.logo {
  width: 120px;
  height: auto;
  margin-bottom: 16px;
}

.matrix-bg {
  background: radial-gradient(circle at 50% 20%, rgba(51, 255, 153, 0.12), rgba(0, 0, 0, 0.92));
  color: #e9ffe3;
}

.matrix-bg::before,
.matrix-bg::after {
  content: '';
  position: absolute;
  inset: -20% 0;
  background-image: repeating-linear-gradient(
      to bottom,
      rgba(0, 255, 140, 0.15) 0,
      rgba(0, 255, 140, 0.15) 2px,
      transparent 2px,
      transparent 16px
    ),
    linear-gradient(120deg, rgba(0, 255, 140, 0.06), transparent 60%),
    linear-gradient(-120deg, rgba(0, 255, 140, 0.06), transparent 60%);
  mix-blend-mode: screen;
  pointer-events: none;
  opacity: 0.6;
  animation: matrixFall 14s linear infinite;
}

.matrix-bg::after {
  animation-duration: 18s;
  animation-direction: reverse;
  opacity: 0.4;
}

@keyframes matrixFall {
  0% {
    transform: translateY(-8%);
  }
  100% {
    transform: translateY(8%);
  }
}
</style>
