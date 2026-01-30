<template>
  <div id="telegram-login"></div>
</template>

<script setup>
import { onMounted } from 'vue'
import { userAuth } from '~/store/userAuth'

const botUsername = 'pureclean_loyality_bot'
const userAuthStore = userAuth()
const { $swal } = useNuxtApp()
const router = useRouter()

onMounted(() => {
  if (!import.meta.client) return

  // MUST be global
  window['onTelegramAuth'] = async (user) => {
    try {
      console.log('Telegram user:', user)
      await userAuthStore.loginWithTelegram(user)
      router.push('/')
    } catch (error) {
      console.error('Telegram login error:', error)
      await $swal.fire({
        title: 'Login failed',
        text: 'Something went wrong. Please try again.',
        icon: 'error',
        timer: 2000
      })
    }
  }

  const container = document.getElementById('telegram-login')
  if (!container || container.children.length > 0) return

  const script = document.createElement('script')
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
</script>
