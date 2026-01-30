<template>
  <div id="telegram-login"></div>
</template>

<script setup>
import { onMounted } from "vue";
import { userAuth } from "~/store/userAuth";

const botUsername = "testpurecleanbot";
const userAuthStore = userAuth();
const { $swal } = useNuxtApp();
const router = useRouter();

onMounted(() => {
  // ✅ Make function GLOBAL
  window.onTelegramAuth = async (user) => {
    console.log("Telegram user:", user);

    try {
      await userAuthStore.loginWithTelegram(user);
      // router.push('/') // optional
    } catch (error) {
      console.error("Login error:", error);
      await $swal.fire({
        title: "Login or Signup failed",
        text: "Oops, something went wrong.",
        icon: "error",
        timer: 2000,
      });
    }
  };

  // ✅ Load Telegram widget AFTER function exists
  const script = document.createElement("script");
  script.src = "https://telegram.org/js/telegram-widget.js?22";
  script.async = true;

  script.setAttribute("data-telegram-login", botUsername);
  script.setAttribute("data-size", "large");
  script.setAttribute("data-radius", "20");
  script.setAttribute("data-userpic", "false");
  script.setAttribute("data-onauth", "onTelegramAuth(user)");
  script.setAttribute("data-request-access", "write");

  document.getElementById("telegram-login").appendChild(script);
});
</script>
