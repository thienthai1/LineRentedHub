<template>
  <div class="container">
    <div v-if="error" class="error">{{ error }}</div>
    <div v-else-if="!initialized">Initializing LIFF...</div>
    <div v-else>
      <div v-if="loggedIn">
        <h1>Welcome to rented hub</h1>
        <div v-if="profile">LINE user: {{ profile.displayName }} ({{ profile.userId }})</div>
        <div v-if="lineliffUser">Stored row: {{ lineliffUser.name }} — role: {{ lineliffUser.role }} — contract: {{ lineliffUser.contract_id }}</div>
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
import axios from 'axios'

const loggedIn = ref(false)
const initialized = ref(false)
const error = ref('')
const profile = ref(null)
const lineliffUser = ref(null)

async function initLiff() {
  try {
    if (!window.liff) {
      error.value = 'LIFF SDK not loaded'
      return
    }
    const liffId = import.meta.env.VITE_CHANNEL_ID
    if (!liffId) {
      error.value = 'VITE_CHANNEL_ID is not set in environment'
      return
    }
    await window.liff.init({ liffId })
    initialized.value = true
    loggedIn.value = window.liff.isLoggedIn()
    if (loggedIn.value) await fetchProfile()
  } catch (e) {
    error.value = e.message || String(e)
  }
}

async function fetchProfile() {
  try {
    if (!window.liff) return
    const p = await window.liff.getProfile()
    profile.value = p
    console.log('Fetching backend profile for userId', p.userId)
    console.log('Backend URL:', import.meta.env.VITE_BACKEND_URL)
    // check backend if this line user exists
    try {
      const backend = import.meta.env.VITE_BACKEND_URL || 'http://localhost:3001'
      const res = await axios.get(`${backend}/api/auth/public/profile/${encodeURIComponent(p.userId)}`)
      if (res.data && res.data.ok && res.data.user) {
        lineliffUser.value = res.data.user
      }
    } catch (e) {
      // ignore
    }
  } catch (e) {
    // ignore profile errors
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
    profile.value = null
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
