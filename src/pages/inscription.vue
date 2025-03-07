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

// Définition du formulaire avec ses champs et valeurs initiales
const form = ref({
  name: '',
  email: '',
  password: '',
  confirmPassword: '',
})

const router = useRouter()

const isPasswordVisible = ref(false)
const isConfirmPasswordVisible = ref(false)

// vérifier si les champs ont été touchés pour la validation
const touchedFields = ref({
  name: false,
  email: false,
  password: false,
  confirmPassword: false,
})

// Gestion des erreurs
const errors = ref<{ name?: string; email?: string; password?: string; confirmPassword?: string }>({})

const authV2LoginMask = useGenerateImageVariant(authV2LoginMaskLight, authV2LoginMaskDark)
const authV2LoginIllustration = useGenerateImageVariant(authV2LoginIllustrationLight, authV2LoginIllustrationDark, authV2LoginIllustrationBorderedLight, authV2LoginIllustrationBorderedDark, true)

// Validation pour chaque field

// champ nom : obligatoire
const validateName = () => {
  if (!touchedFields.value.name)
    return

  if (!form.value.name)
    errors.value.name = 'Le nom est requis'
  else
    errors.value.name = undefined
}

// champ email : obligatoire et format valide
const validateEmail = () => {
  if (!touchedFields.value.email)
    return

  if (!form.value.email)
    errors.value.email = 'L\'email est requis'
  else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(form.value.email))
    errors.value.email = 'Email invalide'
  else
    errors.value.email = undefined
}

// champ mot de passe : obligatoire, longueur minimale de 6 caractères, et confirmation
const validateConfirmPassword = () => {
  if (!touchedFields.value.confirmPassword)
    return
  if (!form.value.confirmPassword)
    errors.value.confirmPassword = 'Veuillez confirmer votre mot de passe'
  else if (form.value.confirmPassword !== form.value.password)
    errors.value.confirmPassword = 'Les mots de passe ne correspondent pas'
  else
    errors.value.confirmPassword = undefined
}

const validatePassword = () => {
  if (!touchedFields.value.password)
    return

  if (!form.value.password)
    errors.value.password = 'Le mot de passe est requis'
  else if (form.value.password.length < 6)
    errors.value.password = 'Le mot de passe doit contenir au moins 6 caractères'
  else
    errors.value.password = undefined

  if (touchedFields.value.confirmPassword)
    validateConfirmPassword()
}

// Fonction pour marquer un champ comme touché et le valider
const handleFieldInput = (field: 'name' | 'email' | 'password' | 'confirmPassword') => {
  touchedFields.value[field] = true

  switch (field) {
    case 'name':
      validateName()
      break
    case 'email':
      validateEmail()
      break
    case 'password':
      validatePassword()
      break
    case 'confirmPassword':
      validateConfirmPassword()
      break
  }
}

// Valider tous les champs
const validateAllFields = () => {
  // Mark all fields as touched
  Object.keys(touchedFields.value).forEach(key => {
    touchedFields.value[key as keyof typeof touchedFields.value] = true
  })

  // exécuter toutes les validations
  validateName()
  validateEmail()
  validatePassword()
  validateConfirmPassword()

  // Return true if no errors
  return !Object.values(errors.value).some(error => error !== undefined)
}

// Soumission du formulaire

const handleRegister = () => {
  if (!validateAllFields())
    return

  const newUser = {
    name: form.value.name,
    email: form.value.email,
    password: form.value.password,
  }

  // Récupérer la liste existante des utilisateurs ou créer un tableau vide d'utilisateur
  const users = JSON.parse(localStorage.getItem('users') || '[]')

  // Vérifier si l'email existe déjà
  if (users.some((user: { email: string }) => user.email === newUser.email)) {
    errors.value.email = 'Cet email est déjà utilisé'

    return
  }

  // Ajouter le nouvel utilisateur à la liste des utilisateurs
  users.push(newUser)

  // Sauvegarder la liste mise à jour
  localStorage.setItem('users', JSON.stringify(users))

  // Stocker également l'utilisateur actuel pour la session

  sessionStorage.setItem('currentUser', JSON.stringify(newUser))
  sessionStorage.setItem('authToken', 'fake-jwt-token')

  // Rediriger vers la page d'accueil après l'inscription
  router.push('/')

  // console.log('User registered:', newUser)
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
                  @input="handleFieldInput('name')"
                  @blur="handleFieldInput('name')"
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
                  @input="handleFieldInput('email')"
                  @blur="handleFieldInput('email')"
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
                  @input="handleFieldInput('password')"
                  @blur="handleFieldInput('password')"
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
                  @input="handleFieldInput('confirmPassword')"
                  @blur="handleFieldInput('confirmPassword')"
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
