<script setup lang="ts">
import { useGenerateImageVariant } from '@/@core/composable/useGenerateImageVariant'
import AuthProvider from '@/views/pages/authentication/AuthProvider.vue'
import authV2LoginIllustrationBorderedDark from '@images/pages/auth-v2-login-illustration-bordered-dark.png'
import authV2LoginIllustrationBorderedLight from '@images/pages/auth-v2-login-illustration-bordered-light.png'
import authV2LoginIllustrationDark from '@images/pages/auth-v2-login-illustration-dark.png'
import authV2LoginIllustrationLight from '@images/pages/auth-v2-login-illustration-light.png'
import authV2LoginMaskDark from '@images/pages/auth-v2-login-mask-dark.png'
import authV2LoginMaskLight from '@images/pages/auth-v2-login-mask-light.png'
import { VNodeRenderer } from '@layouts/components/VNodeRenderer'
import { themeConfig } from '@themeConfig'

import { ref } from 'vue'
import { useRouter } from 'vue-router'

definePage({
  meta: {
    layout: 'blank',
    public: true,
  },
})

const form = ref({
  email: '',
  password: '',
  remember: false,
})

const router = useRouter()

const isPasswordVisible = ref(false)

// Track which fields have been touched
const touchedFields = ref({
  email: false,
  password: false,
})

// voir les erreurs dans le formulaire
const errors = ref<{ email?: string; password?: string }>({})
const authV2LoginMask = useGenerateImageVariant(authV2LoginMaskLight, authV2LoginMaskDark)
const authV2LoginIllustration = useGenerateImageVariant(authV2LoginIllustrationLight, authV2LoginIllustrationDark, authV2LoginIllustrationBorderedLight, authV2LoginIllustrationBorderedDark, true)

// Fonctions de validation pour chaque champ
const validateEmail = () => {
  if (!touchedFields.value.email) return
  
  if (!form.value.email)
    errors.value.email = 'L\'email est requis'
  else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(form.value.email))
    errors.value.email = 'Email invalide'
  else
    errors.value.email = undefined
}

const validatePassword = () => {
  if (!touchedFields.value.password) return

  if (!form.value.password)
    errors.value.password = 'Le mot de passe est requis'
  else
    errors.value.password = undefined
}

// Function to mark field as touched and validate it
const handleFieldInput = (field: 'email' | 'password') => {
  touchedFields.value[field] = true
  
  if (field === 'email')
    validateEmail()
  else if (field === 'password')
    validatePassword()
}

// Validate all fields on form submission
const validateAllFields = () => {
  // Mark all fields as touched
  touchedFields.value.email = true
  touchedFields.value.password = true
  
  // Run all validations
  validateEmail()
  validatePassword()
  
  // Return true if no errors
  return !Object.values(errors.value).some(error => error !== undefined)
}

// Connexion (simulée)
const handleLogin = () => {
  if (!validateAllFields())
    return

  // Récupérer les données utilisateur depuis le localStorage (si l'utilisateur s'est déjà inscrit)
  let userData = null
  try {
    userData = JSON.parse(localStorage.getItem('userData') || '{}')
  } catch (error) {
    userData = {}
  }

  // Vérifie si l'email du login correspond à celui de l'inscription
  if (userData && userData.email === form.value.email) {
    // L'utilisateur a été trouvé, utiliser ses informations existantes
    // Mettre à jour la dernière connexion
    userData.lastLogin = new Date().toISOString()
    localStorage.setItem('userData', JSON.stringify(userData))
  } else {
    // L'utilisateur n'existe pas encore ou utilise un email différent
    // Créer un profil minimal
    const newUserData = {
      email: form.value.email,
      name: form.value.email.split('@')[0], // Nom d'utilisateur par défaut basé sur l'email
      lastLogin: new Date().toISOString()
    }
    localStorage.setItem('userData', JSON.stringify(newUserData))
  }

  // Stocker les informations de connexion dans le localStorage
  localStorage.setItem('authToken', 'fake-jwt-token')
  localStorage.setItem('userEmail', form.value.email)
  localStorage.setItem('rememberMe', JSON.stringify(form.value.remember))

  // Redirection vers le dashboard
  router.push('/')
}
</script>

<template>
  <a href="javascript:void(0)">
    <div class="app-logo auth-logo">
      <VNodeRenderer :nodes="themeConfig.app.logo" />
      <h1 class="app-logo-title">
        {{ themeConfig.app.title }}
      </h1>
    </div>
  </a>

  <VRow
    no-gutters
    class="auth-wrapper"
  >
    <VCol
      md="8"
      class="d-none d-md-flex align-center justify-center position-relative"
    >
      <div class="d-flex align-center justify-center pa-10">
        <img
          :src="authV2LoginIllustration"
          class="auth-illustration w-100"
          alt="auth-illustration"
        >
      </div>
      <VImg
        :src="authV2LoginMask"
        class="d-none d-md-flex auth-footer-mask"
        alt="auth-mask"
      />
    </VCol>
    <VCol
      cols="12"
      md="4"
      class="auth-card-v2 d-flex align-center justify-center"
      style="background-color: rgb(var(--v-theme-surface));"
    >
      <VCard
        flat
        :max-width="500"
        class="mt-12 mt-sm-0 pa-5 pa-lg-7"
      >
        <VCardText>
          <h4 class="text-h4 mb-1">
            Welcome to <span class="text-capitalize">{{ themeConfig.app.title }}! 👋🏻</span>
          </h4>

          <p class="mb-0">
            Please sign-in to your account and start the adventure
          </p>
        </VCardText>

        <VCardText>
          <!-- soumission : appel de la fonction handleLogin -->
          <VForm @submit.prevent="handleLogin">
            <VRow>
              <!-- email -->
              <VCol cols="12">
                <VTextField
                  v-model="form.email"
                  autofocus
                  label="Email"
                  type="email"
                  placeholder="johndoe@email.com"
                  :error-messages="errors.email"
                  @input="handleFieldInput('email')"
                  @blur="handleFieldInput('email')"
                />
              </VCol>

              <!-- password -->
              <VCol cols="12">
                <VTextField
                  v-model="form.password"
                  label="Password"
                  placeholder="············"
                  :type="isPasswordVisible ? 'text' : 'password'"
                  :append-inner-icon="isPasswordVisible ? 'ri-eye-off-line' : 'ri-eye-line'"
                  :error-messages="errors.password"
                  @input="handleFieldInput('password')"
                  @blur="handleFieldInput('password')"
                  @click:append-inner="isPasswordVisible = !isPasswordVisible"
                />

                <!-- remember me checkbox -->
                <div class="d-flex align-center justify-space-between flex-wrap my-6 gap-x-2">
                  <VCheckbox
                    v-model="form.remember"
                    label="Remember me"
                  />

                  <a
                    class="text-primary"
                    href="javascript:void(0)"
                  >
                    Forgot Password?
                  </a>
                </div>

                <!-- login button -->
                <VBtn
                  block
                  type="submit"
                >
                  Login
                </VBtn>
              </VCol>

              <!-- create account -->
              <VCol
                cols="12"
                class="text-body-1 text-center"
              >
                <span class="d-inline-block">
                  New on our platform?
                </span>
                <RouterLink
                  to="inscription"
                  class="text-primary ms-1 d-inline-block text-body-1"
                >
                  Create an account
                </RouterLink>
              </VCol>

              <VCol
                cols="12"
                class="d-flex align-center"
              >
                <VDivider />
                <span class="mx-4 text-high-emphasis">or</span>
                <VDivider />
              </VCol>

              <!-- auth providers -->
              <VCol
                cols="12"
                class="text-center"
              >
                <AuthProvider />
              </VCol>
            </VRow>
          </VForm>
        </VCardText>
      </VCard>
    </VCol>
  </VRow>
</template>

<style lang="scss">
@use "@core/scss/template/pages/page-auth";
</style>
