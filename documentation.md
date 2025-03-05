# Intégration du template

- Exécution de :

```sh
npm install
```

pour l'installation des dépendances du template

- Exécution de :

```sh
npm run dev
```

pour démarrer le projet

# Composant de Connexion (login.vue)

## 🔐 Modifications Principales

- Validation dynamique des champs
- Simulation de la connexion
- Redirection après la connexion

### Validation des champs

- Validation en temps réel des champs
- Contrôle du format d'email
- Vérification de la présence des champs
- Regex pour validation d'email
- Contrôle de la présence des champs
- Gestion des champs "sélectionnés"

### Simulation de connexion

- Authentification basée sur `localStorage`
- Vérification des identifiants
- Génération d'un token de session fictif
- Utilisateurs stockés de manière persistante dans `localStorage`
- Utilisateur courant stocké temporairement dans `sessionStorage`
- Token d'authentification généré

## 🚦 Flux de Connexion

1. Saisie des identifiants
2. Validation dynamique
3. Vérification des credentials
4. Connexion réussie ou échec
5. Redirection ou affichage d'erreur

## 💡 Fonctions Clés

### Validation

- `validateEmail()`: Validation de l'email
- `validatePassword()`: Validation du mot de passe
- `handleFieldInput()`: Gestion des interactions utilisateur pour le remplissage des champs
- `handleLogin()`: Traitement de la connexion

## 🔒 Gestion des Erreurs

- Validation des champs en temps réel
- Messages d'erreur personnalisés
- Gestion des erreurs de connexion globales

# Composant d'Inscription (inscription.vue)

## 🔑 Modifications Principales

### Gestion du Formulaire

- Formulaire complet d'inscription avec validation côté client
- Champs : Nom, Email, Mot de passe, Confirmation du mot de passe

### Validation des Champs

- Validation dynamique en temps réel
- Gestion des erreurs personnalisées pour chaque champ
  - Nom : Champ obligatoire
  - Email : Format et obligation
  - Mot de passe :
    - Minimum 6 caractères
    - Correspondance avec la confirmation

### Stockage des Utilisateurs

- Stockage local des utilisateurs via `localStorage`
- Prévention des doublons d'email
- Génération d'un token de session fictif

## 🚦 Flux d'Inscription

1. Remplissage du formulaire
2. Validation en temps réel des champs
3. Vérification de l'unicité de l'email
4. Création de l'utilisateur
5. Redirection vers la page principale

## 💡 Fonctions Clés

### Validation

- `validateName()`: Validation du nom
- `validateEmail()`: Validation de l'email
- `validatePassword()`: Validation du mot de passe
- `validateConfirmPassword()`: Validation de la confirmation

### Gestion du Formulaire

- `handleFieldInput()`: Gestion des interactions utilisateur
- `handleRegister()`: Soumission et traitement du formulaire

## 🔒 Sécurité et Stockage

### Stockage Local

- Utilisateurs stockés dans `localStorage`
- Utilisateur courant stocké dans `sessionStorage`
- Token d'authentification généré

# Composant App.vue - Configuration Principale de l'Application

## 🔑 Principales Modifications

### Gestion de l'Authentification

- Vérification de l'authentification via `sessionStorage`
- Redirection automatique vers la page de login si l'utilisateur n'est pas connecté
- Protection des routes non authentifiées

## 🛡️ Mécanisme de Protection des Routes

### Logique de Redirection

```typescript
onMounted(() => {
  const isAuthenticated = sessionStorage.getItem('authToken')
  const currentPath = window.location.pathname

  // Si l'utilisateur n'est pas connecté
  if (!isAuthenticated) {
    // Rediriger si l'utilisateur n'est pas sur une page d'authentification
    if (currentPath !== '/login' && currentPath !== '/inscription')
      router.push('/login')
  }
})
```

### Conditions de Redirection

- Vérification du token d'authentification
- Exceptions pour les pages de login et d'inscription

# Composant de profil (UserProfile.vue)

## Modifications principales

- Logique de déconnexion
- Affichage du profile

### Logique de déconnexion
Supprimer la session créer lors de la connexion et rediriger l'utilisateur vers la page de connexion

```typescript
// Fonction pour déconnecter l'utilisateur
const handleLogout = () => {
  sessionStorage.removeItem('authToken')
  sessionStorage.removeItem('currentUser') // Supprime aussi l'utilisateur courant (la session)
  router.push('/login')
}

```

### Affichage du profil


```typescript
onMounted(() => {
  try {
    // Récupérer les données utilisateur depuis sessionStorage
    const userData = JSON.parse(sessionStorage.getItem('currentUser') || '{}')

    // Mettre à jour le nom si disponible
    if (userData && userData.name)
      userName.value = userData.name
  }
  catch (error) {
    console.error('Erreur lors du chargement des données utilisateur:', error)
  }
})
```

Récupéer les informations de l'utilisateur connecté dans le sessionStorage pour les affichées dans le composant.
