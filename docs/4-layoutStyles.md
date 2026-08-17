---
id: styles
title: Les styles
sidebar_label: Les styles
---

# Introduction aux Styles dans React Native

En React Native, les styles sont définis avec l'objet `StyleSheet.create` au lieu d'un fichier `.css`. La syntaxe est similaire au CSS, mais les noms de propriétés sont en camelCase plutôt qu'en kebab-case.

## Définition de styles

Dans le cadre de ce cours, on pourra placer les styles en dessous de la définition de l'interface utilisateur :

```tsx
import { Text, StyleSheet } from "react-native";

type TitreProps = {
  texte: string;
};

export default function Titre({ texte }: TitreProps) {
  {/*on attache le style ici*/}
  return <Text style={styles.titre}>{texte}</Text>;
}

//Définition des styles (en dehors de l'export)
const styles = StyleSheet.create({
  titre: {
    fontSize: 24,
    fontWeight: "bold",
    marginBottom: 12,
    color: "navy",
  },
  /*
  autreStyleIci: {
    ...
  },
  */
});
```

## Syntaxe React Native vs CSS

| CSS               | React Native       |
|-------------------|--------------------|
| margin-bottom     | marginBottom       |
| background-color  | backgroundColor    |
| text-align        | textAlign          |
| font-weight       | fontWeight         |
| justify-content   | justifyContent     |

## Flexbox en React Native

React Native utilise le modèle Flexbox pour la disposition des éléments.

### Propriétés du conteneur

```tsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexDirection: 'row',
    justifyContent: 'center',
    alignItems: 'center',
  },
});
```

### Rappel des propriétés clés

- **flexDirection** : Détermine l’orientation des enfants (`'row'` ou `'column'`).
- **justifyContent** : Contrôle l’alignement des enfants selon la direction principale.
  - `flex-start`, `center`, `flex-end`, `space-between`, `space-around`, `space-evenly`
- **alignItems** : Contrôle l’alignement selon la direction secondaire.
  - `flex-start`, `center`, `flex-end`, `stretch`

## Exemple complet

```tsx
import React from 'react';
import { View, Text, StyleSheet } from 'react-native';

export default function App() {
  return (
    <View style={styles.container}>
      <Text style={styles.title}>Bienvenue !</Text>
      <Text>Voici un exemple de styles en React Native.</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#f0f0f0',
    justifyContent: 'center',
    alignItems: 'center',
  },
  title: {
    fontSize: 20,
    fontWeight: 'bold',
    marginBottom: 10,
  },
});
```

![App avec styles](/img/style.jpg)
