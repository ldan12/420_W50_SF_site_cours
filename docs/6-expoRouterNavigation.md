---
id: navigation
title: La navigation avec Expo Router
sidebar_label: La navigation avec Expo Router
---

# Navigation avec Expo Router

## 1. Principe de base

`expo-router` utilise un système de **fichiers = routes**.  
Chaque fichier placé dans le dossier `app/` devient automatiquement une route accessible dans l'application. 

Par exemple :

```
app/
├─ index.tsx → "/"
├─ about.tsx → "/about"
├─ profile/
│ ├─ index.tsx → "/profile"
│ └─ settings.tsx → "/profile/settings"
```


`index.tsx` étant la route chargée par défaut.

Un peu  à l'image d'un navigateur, `expo-router` utilise un système de Pile (Stack) pour naviguer les routes. Il est donc possible de :

- Ajouter une route sur la stack (**push**)
- Revenir à la route précédente (**back**)
- Remplacer la route actuelle sur la stack (**replace**)

---

## 2. Définition de la Stack

Le fichier `layout.tsx` contient la définition de nos routes. Jusqu'ici laissé vide, le nom du fichier était par défaut affiché dans l'entête de l'écran :

![no title](/img/header-no-title.jpg)

Dorénavant, il sera possible de configurer le texte affiché dans l'entête au chargement de la route. On tentera de respecter l'ordre d'apparition dans la hiérarchie :

```tsx
export default function RootLayout() {
  return (
    <Stack>
      <Stack.Screen name="index" options={{ title: "Accueil" }} />
      <Stack.Screen name="about" options={{ title: "À propos de nous" }} />
      <Stack.Screen name="profile/index" options={{ title: "Mon profil" }} />
      <Stack.Screen name="profile/settings" options={{ title: "Mes paramètres de profil" }} />
    </Stack>
  );
}
```

![with title](/img/header-with-title.jpg)

Le `RootLayout` étant le canvas principal de l'application, il sera est aussi notamment possible de styler l'entête à cet endroit. Voir la documentation à cet effet : https://docs.expo.dev/router/advanced/stack/

## 3. Navigation entre les routes

Il faut tout d'abord importer le router offert par expo :

```tsx
import { router } from "expo-router";
```

### 3.1 Navigation vers une route via un événement

```tsx
import { router } from "expo-router";
import { Button, View } from "react-native";

<View>
  <Button 
    title="click me" 
    onPress={() => router.push("/about")}
    // ou router.replace("/profile")
    // ou router.back()
  />
</View>
```

On peut évidemment définir un méthode pour écouter le `onPress` du bouton et y coder une logique plus évoluée, par exemple vérifier si l'utilisateur est authentifié afin de le diriger vers la page ciblée ou la page d'authentification.


### 3.2 Navigation vers une route via un lien statique

```tsx
import { Link } from "expo-router";

<Link href="/about">À propos</Link>
```

## 4. Routes dynamiques

Il est aussi possible de créer des fichiers avec des noms dynamiques entre crochets :

```
app/
 └─ product/
     └─ [id].js
```

Dans ce cas-ci, la navigation vers cette route s'effectuera de cette façon :

```tsx
router.push({
  pathname: "/product/[id]",
  params: { id: 42 }
});

//ou, considérant que product est un type avec un id :

<Link href={`/product/${product.id}`}>
```

et sera récupéré ainsi dans la route :

```tsx
import { useLocalSearchParams } from "expo-router";

export default function ProductPage() {
  const { id } = useLocalSearchParams();

  return <Text>ID du produit : {id}</Text>;
}
```


## 5. Groupe de routes

Expo Router permet de regrouper plusieurs routes dans un même dossier, **sans que le nom de ce dossier fasse partie de l'URL**.

Pour créer un groupe de routes, on utilise des parenthèses autour du nom du dossier :

```
app/
├── _layout.tsx              ← Root Layout
├── index.tsx
│
└── (tabs)/
    ├── _layout.tsx          ← Layout du groupe
    ├── contact.tsx
    └── about.tsx
```

Le `RootLayout` utilise ici une `Stack` pour gérer la navigation principale :
 
```tsx
import { Stack } from "expo-router";

export default function RootLayout() {
  return (
    <Stack>
      <Stack.Screen
        name="index"
        options={{ title: "Accueil" }}
      />

      <!-- On met le groupe de routes sur la stack-->
      <Stack.Screen
        name="(tabs)"
        options={{ headerShown: false }}
      />
    </Stack>
  );
}
```

On remarque que le groupe de routes (tabs) possède aussi son layout. Le groupe de route peut porter n'importe quel nom et peut lui même être une stack, on utilise ici (tabs) afin d'illustrer que ce groupe de routes peut aussi être disposé sous forme d'onglets navigables dans le bas de l'application. 


```tsx
//Layout du groupe
import { Tabs } from "expo-router";

export default function TabsLayout() {
  return (
    <Tabs>
      <Tabs.Screen
        name="contact"
        options={{ title: "Contactez-nous" }}
      />

      <Tabs.Screen
        name="about"
        options={{ title: "À propos" }}
      />
    </Tabs>
  );
}
```

### 5.1 Navigation d'une route dans un groupe

Il est important de comprendre que (tabs) ne fait pas partie du chemin de la route.

Ainsi, pour naviguer vers la page contact, on utilise simplement :

```tsx
router.push("/contact");
```

et non 

```tsx
router.push("/(tabs)/contact");
```

Le groupe de routes permet donc d'organiser les fichiers sans modifier les chemins utilisés pour naviguer dans l'application.

## 6. En résumé

| Méthode                   | Description                                                         |
| ------------------------- | --------------------------------------------------------------------|
| `router.push("/path")`    | Navigue vers une page en ajoutant à l’historique                    |
| `router.replace("/path")` | Navigue vers une page en remplaçant la page actuelle                |
| `router.back()`           | Retour à la page précédente                                         |
| `useLocalSearchParams()`  | Récupère les paramètres passés par l’URL                            |
| `(groupe)`                | Regroupement de routes, le (groupe ne fait pas partie du chemin)    |
| `_layout.tsx`             | Possible d'avoir un layout dans le groupe pour organiser les routes |


















