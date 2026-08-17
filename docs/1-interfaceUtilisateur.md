---
id: interface-utilisateur
title: Interface utilisateur
sidebar_label: Interface utilisateur
---

# Introduction aux composants de base en React Native

Avant de plonger les interactions d'une application React Native, il est essentiel d'expérimenter quelques composants d'interface graphique que React Native fournit ainsi que leurs propriétés de base (voir la documentation pour la liste complète).

Cette page couvre les composants suivants :

- `View`
- `TextInput`
- `TouchableOpacity`
- `FlatList`

---

## 1. View

### Description

Le composant `View` est l'équivalent d’un `<div>` en HTML. Il sert à organiser et structurer la mise en page.

### Exemple

```tsx
<View style={{ padding: 20 }}>
  <Text>Hello world</Text>
</View>
```

---

## 2. TextInput

### Description

Permet à l’utilisateur de saisir du texte, comme un champ de formulaire.

### Exemple

```tsx
<TextInput
  value={/*La valeur actuelle du champ*/}
  onChangeText={/*action que l'on fera sur cet événement*/}
  placeholder="Entrez quelque chose"
  style={/*Les styles que l'on appliquera*/}
/>
```

### Propriétés (Props) principales

- `value` : la valeur actuelle du champ
- `onChangeText` : fonction déclenchée quand le texte change
- `placeholder` : texte d’aide grisé
- `style` : styles (bordure, couleur, etc.)

---

## 3. TouchableOpacity

### 🔹 Description

Composant qui rend son enfant “cliquable” (comme un). Il est souvent utilisé avec `Text` à l’intérieur.

### Exemple

```tsx
<TouchableOpacity
  onPress={handleClick}
  style={{ backgroundColor: "blue", padding: 10 }}>

  <Text style={{ color: "white" }}>Clique-moi</Text>
  
</TouchableOpacity>
```

### Propriétés (Props) principales

- `onPress` : fonction appelée quand on clique
- `style` : styles appliqués à la zone cliquable

---

## 4. FlatList

### 🔹 Description

Permet d’afficher une liste déroulante performante pour de grands ensembles de données.

### Exemple

```tsx
<FlatList
  data={["Joe", "Jack", "William", "Averell"]}
  keyExtractor={(item, index) => index.toString()}
  renderItem={({ item }) => <Text>{item}</Text>}
/>
```

### Propriétés (Props) essentielles

- `data` : tableau des éléments à afficher, peut être une donnée simple (voir exemple) ou un objet plus complexe
- `keyExtractor` : fonction qui retourne une clé unique par item (requis !). Pour une donnée simple, utiliser un index. Si on charge la liste avec un objet plus complexe et qu'il contient une propriété unique (ex : id), il est fortement recommandé de l'utiliser comme _KeyExtractor_
- `renderItem` : fonction qui retourne le composant à afficher pour **chaque élément de la liste**

---

### Importation

Dans tous les cas, n’oubliez pas d’importer les composants depuis `react-native` dans la section des imports avant de les utiliser :

```tsx
import {
  View,
  Text,
  TextInput,
  TouchableOpacity,
  FlatList,
} from "react-native";
```
