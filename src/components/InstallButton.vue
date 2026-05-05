<template>
  <div v-if="deferredPrompt" class="install-button">
    <button @click="installApp" class="install-btn">Instalar App</button>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const deferredPrompt = ref(null)

onMounted(() => {
  window.addEventListener('beforeinstallprompt', (e) => {
    e.preventDefault()
    deferredPrompt.value = e
  })
})

function installApp() {
  if (deferredPrompt.value) {
    deferredPrompt.value.prompt()
    deferredPrompt.value.userChoice.then((choiceResult) => {
      if (choiceResult.outcome === 'accepted') {
        console.log('User accepted the install prompt')
      } else {
        console.log('User dismissed the install prompt')
      }
      deferredPrompt.value = null
    })
  }
}
</script>

<style scoped>
.install-button {
  margin-top: 20px;
  text-align: center;
}

.install-btn {
  padding: 10px 20px;
  background-color: #4a90d9;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 1rem;
}

.install-btn:hover {
  background-color: #357abd;
}
</style>
