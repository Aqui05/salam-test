<script setup lang="ts">
import ScrollToTop from '@core/components/ScrollToTop.vue'
import initCore from '@core/initCore'
import { initConfigStore, useConfigStore } from '@core/stores/config'
import { hexToRgb } from '@core/utils/colorConverter'
import { onMounted } from 'vue'
import { useRouter } from 'vue-router'
import { useTheme } from 'vuetify'

const { global } = useTheme()
const router = useRouter()
const configStore = useConfigStore()

initCore()
initConfigStore()

onMounted(() => {
  const isAuthenticated = sessionStorage.getItem('authToken')

  // Récupérer le chemin actuel

  const currentPath = window.location.pathname

  // console.log(currentPath)

  if (!isAuthenticated) {
    // Rediriger uniquement si l'utilisateur n'est pas sur une page d'authentification
    if (currentPath !== '/login' && currentPath !== '/inscription')
      router.push('/login')
  }
})
</script>

<template>
  <VLocaleProvider :rtl="configStore.isAppRTL">
    <VApp :style="`--v-global-theme-primary: ${hexToRgb(global.current.value.colors.primary)}`">
      <RouterView />
      <ScrollToTop />
    </VApp>
  </VLocaleProvider>
</template>
