---
id: context
title: Les contextes
sidebar_label: Les contextes
---

# Comprendre les Contextes en React

## Pourquoi utiliser un contexte ?

En React, les **props** sont le mécanisme principal pour partager des données entre composants. Cependant, lorsqu'une donnée doit être utilisée par de nombreux composants à différents niveaux, le "prop drilling" (passage répété de props) devient lourd et peu maintenable.

C'est là qu'intervient le **Context API** de React. Il permet de partager des données à travers toute l’application sans avoir à les transmettre manuellement à chaque niveau.

---

## Exemple : Contexte de langue

Imaginons une application multilingue où l’utilisateur peut basculer entre le français et l'anglais. Au lieu de passer la langue en props à chaque composant pour qu'il se render correctement (lourd et redondant), on va utiliser un contexte pour la langue. l'idée est que chaque composant pourra interpréter le contexte global de langue pour se configurer adéquatement.

### Étape 1 : Préparation des types

Dans cet exemple, nous aurons besoin d'un type de base pour représenter la langue :

```ts
export type Language = "en" | "fr";
```

Il faudra aussi décrir ce que le contexte va exposer. Dans cet exemple, la langue courante et la possibilité de la changer :

```ts
export interface LanguageContextType {
  language: Language;
  toggleLanguage: () => void;
}
```

**Note** : On pourrait exposer d'autres élements (états ou méthodes) qui seront gérés par le context. Par exemple, un contexte d'authentification pourrait nous exposer l'état authentifié ou non, ainsi que les méthodes usuelles d'authentification (signup, signin, logout, etc.). Il faudrait décrire tout ceci dans l'interface ci-haut.

### Étape 2 : Créer le contexte

L'idée est de venir **englober** les composants de notre application par le contexte. Nous verrons plus tard comment ceci se traduit dans le `Layout` de notre application.

Dans un répertoire nommé `contexts`, situé au même niveau que `app`, on créer le fichier de contexte, nommé en fonction de ce qu'il gère, ici **LanguageContext.tsx**

```tsx
// LanguageContext.tsx

//Création du contexte
export const LanguageContext = createContext<LanguageContextType | null>(null);

//Le provider du contexte
export const LanguageContextProvider = ({ children }: { children: React.ReactNode }) => {

  //l'état du langage lisible par ceux qui chargeront le contexte
  const [language, setLanguage] = useState<Language>("en");

  //la méthode qui pourra être utilisée par ceux qui chargeront le contexte
  const toggleLanguage = () => {
    setLanguage((prev) => (prev === "en" ? "fe" : "en"));
  };

  /*
  LanguageContext.Provider est le composant spécial créé par createContext plus haut (Chaque Context en React crée un Provider et un Consumer.)
  
  Le Provider enveloppe un arbre de composants et leur permet d’accéder aux éléments qu'il expose.
  
  Concrètement : tout ce qui est dans {children} sera capable d’utiliser useLanguage() (voir plus bas) pour accéder à language et toggleLanguage.
  
  Sans ce Provider, useLanguage() renverrait null et le guard (if (!value)) lancerait une erreur si un composant tente de l'accéder, mais qu'il n'est pas englobé par le contexte.
  */

  return (
    <LanguageContext.Provider value={{ language, toggleLanguage }}>
      {children}
    </LanguageContext.Provider>
  );
};
```

### Étape 3 : Créer un hook permettant d'obtenir le contexte

Dans un dossier nommée `hooks`, on créer un fichier selon le nom du contexte (ici Language) : *useLangage.tsx*

Ce fichier retournera le contexte :

```tsx
// useLangage.tsx
import { LangageContext } from "@/contexts/LangageContext";
import { useContext } from "react";

// Hook qui permet l'utilisation du contexte
export const useLanguage = () => {
  const value = useContext(LanguageContext);

  if (!value) {
    throw new Error(
      "useLanguage doit être contenu dans un LanguageContextProvider"
    );
  }

  return value;
};
```

### Étape 4: Rendre le contexte disponible dans l'application

L'idée est d'englober les composants de notre application dans le contexte. Ceci se fait dans le `_layout.tsx` de notre application qui, de base, ressemble à ceci :

```tsx
import { Stack } from "expo-router";

function RootLayout() {
    return (
    <Stack/>
  );
}
```

Mais qui prendra la forme suivante pour exposer le contexte aux pages et composants de l'application :

```tsx
import { LanguageContextProvider } from "@/contexts/LanguageContext";
import { Stack } from "expo-router";

function MainLayout() {
    return (
    <Stack/>
  );
}


export default function RootLayout() {
  return (
    <LanguageContextProvider>
      <MainLayout />
    </LanguageContextProvider>
  );
}
```

On voit ici le principe du *children* qui aura accès au contexte, si on englobe pas le **MainLayout** du contexte, on recevra l'exception contenue dans le *hook* à l'utilisation.

Note : Si on a d'autres contextes dans notre application, on englobe le bloc actuel avec ce contexte et ainsi de suite.

### Étape 5: Utiliser le contexte dans l'application

```tsx
// LanguageSelector.tsx
import { useLangage } from "@/hooks/useLanguage";

const LanguageSelector = () => {
  //Appel du hook : Accès aux éléments du contexte (le langage actuel et la possibilité de le changer)
  const { language, switchLanguage } = useLanguage();

  return (
    <div>
      <p>Langue actuelle : {language}</p>
      <button onClick={() => switchLanguage("fr")}>Français</button>
      <button onClick={() => switchLanguage("en")}>English</button>
    </div>
  );
};

export default LanguageSelector;
```

**Ce qui se passe ici :**

- Le hook livre le contexte, ceci nous permet d’accéder directement à `language` et `switchLanguage` fournis par le `LanguageProvider`.
- Le composant peut ainsi afficher et modifier la langue sans avoir besoin de recevoir de props.

---

## Quand utiliser un contexte ?

Le contexte est utile quand :

- Une donnée est utilisée à plusieurs endroits éloignés dans la hiérarchie des composants.
- La donnée est "globale" (ex. : thème, langue, utilisateur connecté, préférences).

Évitez le contexte si la donnée est utilisée uniquement par quelques composants proches : les props suffisent.
