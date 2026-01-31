<template>
  <div id="telegram-login"></div>
</template>

<script setup>
import { onMounted, onBeforeUnmount } from 'vue'

const BOT_USERNAME = 'samplebot' // without @

onMounted(() => {
  // 1️⃣ Expose function globally (Telegram needs window scope)
  window.onTelegramAuth = (user) => {
    alert(
      `Logged in as ${user.first_name} ${user.last_name ?? ''} ` +
      `(${user.id}${user.username ? ', @' + user.username : ''})`
    )

    // TODO: send user to backend
    // fetch('/api/auth/telegram', {...})
  }

  // 2️⃣ Remove old script if exists (important for SPA navigation)
  const oldScript = document.getElementById('telegram-widget')
  if (oldScript) oldScript.remove()

  // 3️⃣ Create script AFTER DOM is ready
  const script = document.createElement('script')
  script.id = 'telegram-widget'
  script.src = 'https://telegram.org/js/telegram-widget.js?22'
  script.async = true

  script.setAttribute('data-telegram-login', BOT_USERNAME)
  script.setAttribute('data-size', 'large')
  script.setAttribute('data-request-access', 'write')
  script.setAttribute('data-onauth', 'onTelegramAuth(user)')

  document.getElementById('telegram-login').appendChild(script)
})

onBeforeUnmount(() => {
  // cleanup (optional but good practice)
  delete window.onTelegramAuth
})
</script>
