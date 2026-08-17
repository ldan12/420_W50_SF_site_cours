---
id: use-state
title: Le Hook useState
sidebar_label: Le Hook useState
---


# Le hook `useState`

## Qu'est-ce que `useState` ?

`useState` est un **hook** de React qui permet à un composant fonctionnel de gérer un **état local**.

C’est comme une **mémoire interne** du composant, qui peut changer au fil du temps (par exemple : un compteur, un champ de formulaire, un booléen pour afficher/masquer un élément, etc.).

---

## Syntaxe de base

```js
const [valeur, setValeur] = useState(valeurInitiale);
```

- `valeur` : la variable d’état (lecture)
- `setValeur` : fonction pour **mettre à jour** la valeur
- `valeurInitiale` : valeur initiale à la première exécution du composant

> Chaque fois que `setValeur(...)` est appelé, le composant est **re-rendu automatiquement**.

---

## Exemple 1 : Compteur simple

```tsx
import React, { useState } from "react";
import { View, Text, Button } from "react-native";

export default function Compteur() {
  const [compte, setCompte] = useState(0);

  return (
    <View>
      <Text>Valeur : {compte}</Text>
      <Button title="Incrémenter" onPress={() => setCompte(compte + 1)} />
    </View>
  );
}
```

### Remarques / Rappels


Un instant ! Nous avons fait la déclaration en const, mais la valeur peut changer ??

Oui, même si la **valeur** de compte change au fil du temps (à chaque appel à setCompte), **la référence à la variable** ne change pas. C’est ça qui permet d’utiliser const ici, et c'est recommandé de le faire.

- `const` signifie que **la variable ne peut pas être réassignée à un nouvel objet ou une nouvelle valeur**.
- Mais cela **ne veut pas dire que la valeur pointée ne peut pas changer** (surtout s'il s'agit d'un objet, ou dans ce cas, d'un hook contrôlé par React).

**Ce que fait réellement React :**

- Au moment où l'on fait `const [compte, setCompte] = useState(0)`, React garde compte à jour **entre chaque rendu du composant.**
- À chaque fois que `setCompte(...)` est appelé, React :

1. Met à jour **l’état interne** du composant.
2. \*\*Re-render le composant.
3. Redonne une **nouvelle valeur mise à jour** à compte dans le useState(...) du nouveau rendu.

Mais la déclaration elle-même (`const [compte, setCompte] = ...)` ne change pas. Elle est réévaluée à chaque rendu, mais **React se charge de fournir la nouvelle valeur**.

---

Un autre instant ! Nous sommes en Typescript, ne faut-il pas indiquer le **type** dans le `useState` ?

```tsx
const [compte, setCompte] = useState<string>(0);
```

Pour les données de départ simple, on peut le laisser ainsi car Typescript va inférer le type et cela va fonctionner. Dès que l'on manipulera des types plus évolués, **il sera fortement recommandé, voire essentiel de le spécifier** :

```tsx
//Si l'on veut maintenir une liste des noms des cours de session 5
//La liste est ici vide dans l'état de départ :
const [coursSession5, setCoursSession5] = useState<string[]>([]);
```

## Exemple 2 : Champ de texte

```tsx
import React, { useState } from "react";
import { View, TextInput, Text } from "react-native";

export default function SaisieTexte() {
  const [nom, setNom] = useState("");

  return (
    <View>
      <TextInput
        placeholder="Entrez votre nom"
        value={nom}
        onChangeText={setNom}
      />
      <Text>Bonjour, {nom}!</Text>
    </View>
  );
}
```
Ici, puisque setNom est une **fonction qui prend une string et l'injecte dans le state**, c'est tout à fait compatible avec :


```tsx
onChangeText={setNom}
```

La version plus "verbeuse" serait celle-ci :

```tsx
onChangeText={(text) => setNom(text)}
```

---

## Attention

- `useState` **ne remplace pas** la gestion d’état globale (sauvegarde entre 2 exécutions).
- On dira que l’état contrôlé par le `useState` est **local au composant**.

---

## Erreurs fréquentes à éviter

### Mauvaise mise à jour directe :

```js
compte = compte + 1; // Ceci ne fait rien de concret !
```

### Bonne méthode :

```js
setCompte(compte + 1);
```

---

## Truc : mise à jour basée sur l'état précédent

```js
setCompte((prev) => prev + 1);
```

Utiliser cette forme évite peut éviter des comportements indédirables lorsqu’on effectue plusieurs mises à jour d’affilée.

---
