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
import { ref, watch } from 'vue'
import { useRouter } from 'vue-router'

definePage({
  meta: {
    layout: 'blank',
    public: true,
  },
})

const form = ref({
  name: '',
  email: '',
  password: '',
  confirmPassword: '',
})

const router = useRouter()

const isPasswordVisible = ref(false)
const isConfirmPasswordVisible = ref(false)

// Gestion des erreurs
const errors = ref<{ name?: string; email?: string; password?: string; confirmPassword?: string }>({})

const authV2LoginMask = useGenerateImageVariant(authV2LoginMaskLight, authV2LoginMaskDark)
const authV2LoginIllustration = useGenerateImageVariant(authV2LoginIllustrationLight, authV2LoginIllustrationDark, authV2LoginIllustrationBorderedLight, authV2LoginIllustrationBorderedDark, true)

// Validation dynamique des champs
watch(form.value, () => {
  errors.value = {}

  if (!form.value.name)
    errors.value.name = 'Le nom est requis'

  if (!form.value.email)
    errors.value.email = 'L\'email est requis'
  else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(form.value.email))
    errors.value.email = 'Email invalide'

  if (!form.value.password)
    errors.value.password = 'Le mot de passe est requis'
  else if (form.value.password.length < 6)
    errors.value.password = 'Le mot de passe doit contenir au moins 6 caractères'

  if (!form.value.confirmPassword)
    errors.value.confirmPassword = 'Veuillez confirmer votre mot de passe'
  else if (form.value.confirmPassword !== form.value.password)
    errors.value.confirmPassword = 'Les mots de passe ne correspondent pas'
})

// Soumission du formulaire
const handleRegister = () => {
  if (Object.keys(errors.value).length > 0)
    return

  // Simuler un enregistrement et rediriger
  localStorage.setItem('authToken', 'fake-jwt-token')
  router.push('/') // Redirection vers la page de connexion après inscription
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
            Create an account on <span class="text-capitalize">{{ themeConfig.app.title }}! 🚀</span>
          </h4>
          <p class="mb-0">
            Please fill in the details below to sign up.
          </p>
        </VCardText>

        <VCardText>
          <VForm @submit.prevent="handleRegister">
            <VRow>
              <!-- Nom -->
              <VCol cols="12">
                <VTextField
                  v-model="form.name"
                  label="Nom"
                  placeholder="John Doe"
                  :error-messages="errors.name"
                />
              </VCol>

              <!-- Email -->
              <VCol cols="12">
                <VTextField
                  v-model="form.email"
                  label="Email"
                  type="email"
                  placeholder="johndoe@email.com"
                  :error-messages="errors.email"
                />
              </VCol>

              <!-- Mot de passe -->
              <VCol cols="12">
                <VTextField
                  v-model="form.password"
                  label="Mot de passe"
                  placeholder="············"
                  :type="isPasswordVisible ? 'text' : 'password'"
                  :append-inner-icon="isPasswordVisible ? 'ri-eye-off-line' : 'ri-eye-line'"
                  :error-messages="errors.password"
                  @click:append-inner="isPasswordVisible = !isPasswordVisible"
                />
              </VCol>

              <!-- Confirmation du mot de passe -->
              <VCol cols="12">
                <VTextField
                  v-model="form.confirmPassword"
                  label="Confirmer le mot de passe"
                  placeholder="············"
                  :type="isConfirmPasswordVisible ? 'text' : 'password'"
                  :append-inner-icon="isConfirmPasswordVisible ? 'ri-eye-off-line' : 'ri-eye-line'"
                  :error-messages="errors.confirmPassword"
                  @click:append-inner="isConfirmPasswordVisible = !isConfirmPasswordVisible"
                />
              </VCol>

              <!-- Bouton S'inscrire -->
              <VCol cols="12">
                <VBtn
                  block
                  type="submit"
                >
                  S'inscrire
                </VBtn>
              </VCol>

              <!-- Déjà un compte ? -->
              <VCol
                cols="12"
                class="text-body-1 text-center"
              >
                <span class="d-inline-block">Vous avez déjà un compte ?</span>
                <RouterLink
                  to="/login"
                  class="text-primary ms-1 d-inline-block text-body-1"
                >
                  Se connecter
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

              <!-- Auth providers -->
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
