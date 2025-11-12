<script setup lang="ts">
import MyTasks from './components/MyList.vue'
import logo from '@/assets/todolisteLogo.png'
</script>

<template>
  <main>
    <!-- Logo -->
    <img :src="logo" alt="Mein Logo" class="logo" />
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
  color: var(--color-heading);
  margin-bottom: 24px;
}

/* Neues Logo-Styling */
.logo {
  width: 120px; /* Logo-Größe anpassen */
  height: auto;
  margin-bottom: 16px; /* Abstand unter dem Logo */
}

</style>
