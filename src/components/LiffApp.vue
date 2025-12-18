<template>
  <div class="container">
    <div v-if="error" class="error">{{ error }}</div>
    <div v-else-if="!initialized">Initializing LIFF...</div>
    <div v-else>
      <div v-if="loggedIn">
        <h1>Welcome to rented hub</h1>
        <button @click="logout">Logout</button>
      </div>
      <div v-else>
        <button @click="login">Login with LINE</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const loggedIn = ref(false)
const initialized = ref(false)
const error = ref('')

async function initLiff() {
  try {
    if (!window.liff) {
      error.value = 'LIFF SDK not loaded'
      return
    }
    // const liffId = import.meta.env.VITE_CHANNEL_ID
    const liffId = '2008727136' // Temporary hardcode for testing
    if (!liffId) {
      error.value = 'VITE_CHANNEL_ID is not set in environment'
      return
    }
    await window.liff.init({ liffId })
    initialized.value = true
    loggedIn.value = window.liff.isLoggedIn()
  } catch (e) {
    error.value = e.message || String(e)
  }
}

function login() {
  try {
    window.liff.login()
  } catch (e) {
    error.value = e.message || String(e)
  }
}
function logout() {
  try {
    window.liff.logout()
    loggedIn.value = false
  } catch (e) {
    error.value = e.message || String(e)
  }
}

onMounted(() => {
  initLiff()
})
</script>

<style scoped>
.container {
  font-family: Arial, Helvetica, sans-serif;
  padding: 2rem;
  display: flex;
  flex-direction: column;
  gap: 1rem;
}
button {
  padding: 0.5rem 1rem;
  background: #00c300;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}
.error {
  color: red;
}
</style>
