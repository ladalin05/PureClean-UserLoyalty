<template>
  <div id="telegram-login"></div>
</template>

<script setup>
import { onMounted, onBeforeUnmount } from 'vue'
import { userAuth } from '~/store/userAuth'

const botUsername = 'pureclean_loyality_bot'
const userAuthStore = userAuth()
const { $swal } = useNuxtApp()
const router = useRouter()

onMounted(() => {
  if (!import.meta.client) return

  // Make callback global
  window.onTelegramAuth = async (user) => {
    const username = user.username || user.first_name + user.last_name; // Fallback to first name if username is not available
    const telegram_id = String(user.id); // Telegram user ID
    console.log("Telegram user:", user);
    const profile_picture = user.photo_url || "";
    console.log("Profile picture URL:", profile_picture);
    try {
      await userAuthStore.login(username, telegram_id, profile_picture);
    } catch (error) {
      console.error("Login error:", error);
      await $swal.fire({
        title: 'Login or Signup failed',
        text: 'Opps have something wrong. Let contact us!',
        icon: 'error',
        confirmButtonText: 'OK',
        timer: 2000
      });
    }
  };

  const container = document.getElementById('telegram-login')
  if (!container) return

  // Remove old script (important for SPA navigation)
  const oldScript = document.getElementById('telegram-widget')
  if (oldScript) oldScript.remove()

  // Create new script
  const script = document.createElement('script')
  script.id = 'telegram-widget' // add ID for easy removal
  script.src = 'https://telegram.org/js/telegram-widget.js?22'
  script.async = true
  script.setAttribute('data-telegram-login', botUsername)
  script.setAttribute('data-size', 'large')
  script.setAttribute('data-radius', '20')
  script.setAttribute('data-userpic', 'false')
  script.setAttribute('data-request-access', 'write')
  script.setAttribute('data-onauth', 'onTelegramAuth(user)')

  container.appendChild(script)
})

onBeforeUnmount(() => {
  // Clean up global callback
  delete window.onTelegramAuth
})
</script>
