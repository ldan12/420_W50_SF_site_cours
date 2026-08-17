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

Dans un répertoire nommé `context`, situé au même niveau que `app`, on créer le fichier de contexte, nommé en fonction de ce qu'il gère, ici **LanguageContext.tsx**

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
### Étape 3: Utiliser le context

Tel que mentionné, l'idée est d'englober les composants de notre application du contexte. Ceci se fait dans le `_layout.tsx` de notre application qui, de base, ressemble à ceci :


///////HERE




```jsx
// LanguageSelector.js
import { useContext } from "react";
import { LanguageContext } from "./LanguageContext";

const LanguageSelector = () => {
  const { language, switchLanguage } = useContext(LanguageContext);

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

🔍 **Ce qui se passe ici :**

- `useContext(LanguageContext)` : Permet d’accéder directement à `language` et `switchLanguage` fournis par le `LanguageProvider`.
- Le composant peut ainsi afficher et modifier la langue sans avoir besoin de recevoir de props.

---

### Étape 3 : Intégrer dans l’application

```jsx
// App.js
import { LanguageProvider } from "./LanguageContext";
import LanguageSelector from "./LanguageSelector";

function App() {
  return (
    <LanguageProvider>
      <h1>Bienvenue dans mon application</h1>
      <LanguageSelector />
    </LanguageProvider>
  );
}

export default App;
```

Ici, toute l’application est englobée dans le `LanguageProvider`, ce qui permet à **tous les composants enfants** d’accéder à la langue.

---

## Quand utiliser un contexte ?

👉 Le contexte est utile quand :

- Une donnée est utilisée à plusieurs endroits éloignés dans la hiérarchie des composants.
- La donnée est "globale" (ex. : thème, langue, utilisateur connecté, préférences).

❌ Évitez le contexte si la donnée est utilisée uniquement par quelques composants proches : les props suffisent.

---

## Résumé

- `createContext` : Crée un contexte.
- `Provider` : Fournit la valeur aux composants enfants.
- `useContext` : Permet de consommer facilement la valeur du contexte.

Avec le **LanguageContext**, nous avons vu comment partager une **langue courante** dans toute l’application sans avoir à passer des props partout.

---

✅ Le Context API est une solution élégante pour centraliser des états globaux comme le thème, la langue ou encore des paramètres utilisateur.
