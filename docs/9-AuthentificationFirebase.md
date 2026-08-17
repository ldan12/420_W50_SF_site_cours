---
id: firebase
title: Authentification Firebase
sidebar_label: Authentification Firebase
---

# Authentification avec Firebase

## Mise en place de la configuration

Au moment de lire ces lignes, vous avez déjà créé un projet **Firebase** nommé `ExerciceFirebase` dans le tutoriel de configuration. Copiez sa configuration en accédant à ses paramètres (voir tutoriel).

**Important** : Une configuration **Firebase** est pour un projet en particulier et non pour le compte en général. Dans les travaux subséquents, vous créerez d'autres projets **Firebase**, il sera important de copier la configuration du bon projet !

À la racine de votre application React Native (au même niveau que `app`, pas dans `app`), créez un fichier nommé `firebaseConfig.ts`

Les *imports* seront les suivants :

```tsx
import { initializeApp } from "firebase/app";
import { initializeAuth, getReactNativePersistence } from "firebase/auth";
import AsyncStorage from "@react-native-async-storage/async-storage";
```

Ensuite, le bloc de configuration sera la vôtre. Nous allons immédiatement isoler les valeurs dans des variables d'environnement :

```tsx
const firebaseConfig = {
  apiKey: process.env.EXPO_PUBLIC_FIREBASE_API_KEY,
  authDomain: process.env.EXPO_PUBLIC_FIREBASE_AUTH_DOMAIN,
  projectId: process.env.EXPO_PUBLIC_FIREBASE_PROJECT_ID,
  storageBucket: process.env.EXPO_PUBLIC_FIREBASE_STORAGE_BUCKET,
  messagingSenderId: process.env.EXPO_PUBLIC_FIREBASE_MESSAGING_SENDER_ID,
  appId: process.env.EXPO_PUBLIC_FIREBASE_APP_ID,
};
```

et dans un nouveau fichier `.env`, lui aussi au même niveau que `app`:

```env
EXPO_PUBLIC_FIREBASE_API_KEY=/*votre clé*/
EXPO_PUBLIC_FIREBASE_AUTH_DOMAIN=/*votre domaine*/
EXPO_PUBLIC_FIREBASE_PROJECT_ID=/*votre projet*/
EXPO_PUBLIC_FIREBASE_STORAGE_BUCKET=/*votre bucket*/
EXPO_PUBLIC_FIREBASE_MESSAGING_SENDER_ID=/*votre sender id*/
EXPO_PUBLIC_FIREBASE_APP_ID=/*votre app id*/
```

Terminez le fichier de configuration **Firebase** avec ces instructions:

```tsx
const app = initializeApp(firebaseConfig);

export const auth = initializeAuth(app, {
  persistence: getReactNativePersistence(AsyncStorage),
});
```

On voit notamment que l'authentification **Firebase** utilisera le storage local pour persister l'utilisateur connecté.

## Troubleshooting (optionnel)

Si l'import suivant vous cause problème :

```tsx
import { initializeAuth, getReactNativePersistence } from "firebase/auth";
```

Remplacez le contenu du fichier `tsconfig.json` par :

```tsx
{
  "extends": "expo/tsconfig.base",
  "compilerOptions": {
    "strict": true,
    "paths": {
      "@/*": [
        "./*"
      ],
      "@firebase/auth": ["./node_modules/@firebase/auth/dist/index.rn.d.ts"]
    }
  },
  "include": [
    "**/*.ts",
    "**/*.tsx",
    ".expo/types/**/*.ts",
    "expo-env.d.ts"
  ]
}
```

## Context d'authentification

La mise en place du contexte d'authentification est similaire aux autres contextes mis en place dans le cadre du cours. On y retrouvera notamment un **statut authentifié (ou non)** ainsi que les méthodes usuelles (signup, signin, signout).

Le contexte doit contenir ce `useEffect` crucial qui prend effet au premier rendu du contexte:

```tsx
useEffect(() => {
  const unsub = onAuthStateChanged(auth, (user) => {
    if (user) {
      setIsAuthenticated(true);
    } else {
      setIsAuthenticated(false);
    }
  });
  return unsub;
}, []);
```

Le paramètre `auth` fait référence à la variable déclarée dans le fichier de configuration de Firebase codé plus haut.

Il permet de programmer un écouteur de statut d'authentification qui vit **tant et aussi longtemps que le contexte n'est pas rechargé** (rappel : useEffect avec return).

La mécanique est simple, aussitôt que le statut de l'authentification change, cet écouteur est appelé. si un `user` est récupéré, le statut est inscrit à "authentifié", le cas contraire si `user` est null.

En bénéficiant de la persistence, il est possible qu'un `user` soit présent au démarrage du contexte, provoquant un `onAuthStateChanged`, ce qui nous amènerait directement à la partie authentifiée de l'application.

Le reste du contexte est tel que vu au cours.

## Routage de base de l'application

La composition du layout de base est identique à celle vue auparavant, elle prévoira cependant une mécanique de routage (avec **expo router**) en réaction au changement d'authentification dans l'application :

```tsx
function MainLayout() {
  //Utilisation du hook d'authentification
  //Utilisation du routeur expo
  //Utilisation des segments (pour inspecter la composition de la route courante)
  //https://docs.expo.dev/versions/latest/sdk/router/#usesegments
  
  
  
  //Ici, un useEffect basé sur l'état authentifié du contexte :
  //Voir plus bas pour les règles de routage

  //Ici un activity indicator si le statut d'authentification est undefined ...

  //... ou bien le chargement de la Stack de layout
}

export default function RootLayout() {
  return (
    <AuthContextProvider>
      <MainLayout />
    </AuthContextProvider>
  );
}
```

### Règles de routage dans le useEffect

En utilisant les segments de route :

1. Si le *state* est `undefined`, on ne fait rien dans le `useEffect`.
2. Si le *state* est **authentifié** et qu’on n’est pas déjà dans une route `(auth)`, on navigue vers l’index (`/`).
   - Cas classique : un utilisateur s’inscrit ou se connecte depuis une page publique.
   - Avantage : Par exemple, s’il est déjà dans `(auth)` et qu’il met à jour son profil (changement dans le *user*, ce qui redéclenchera ici un `onAuthStateChanged` avec un état toujours authentifié), on ne naviguera pas inutilement.
3. Si l’utilisateur n’est **pas authentifié**, on navigue vers **/login**.
