---
id: use-effect
title: Le Hook useEffect
sidebar_label: Le Hook useEffect
---

# Le Hook `useEffect`

`useEffect` est un **hook** qui permet d’exécuter du code **après** le rendu du composant. Il est utilisé pour :

- Exécuter du code une seule fois (ex : appel API)
- Réagir à un changement de variable
- Nettoyer des effets (ex : intervalle, listener)

---

## Syntaxe de base

```tsx
import { useEffect } from 'react';

useEffect(() => {
  // Code à exécuter après le rendu
}, [/* dépendances */]);
```
--- 

## Les dépendances

`useEffect` se déclenche systématiquement après le **premier rendu** du composant. Il est par contre possible de le déclencher à d'autres moments en spécifiant des **dépendances**. Voici les 3 possibilités :


### Cas 1 : Tableau vide

```tsx
useEffect(() => {
  console.log("Ce code s'exécute UNE SEULE FOIS");
}, []);
```

- Le code s'exécute **une seule fois** après le premier rendu.
- Cas typique : appel API au chargement du composant.


### Cas 2 : Avec dépendance [maVariable]

```tsx
useEffect(() => {
  console.log("maVariable a changé :", maVariable);
}, [maVariable]);
```

Le code s’exécute :

- après le premier rendu
- puis chaque fois que `maVariable` change

Très utile pour déclencher une action en réponse à un changement de valeur.

Note : Il est aussi possible de préciser d'autres variables dans le tableau des dépendances si l'effet est applicable au changement de valeur de l'une **ou** l'autre des variables.

Pensons au retrait/ajout d'éléments dans une liste et à un filtre de recherche sur cette liste. Dans les 2 cas, le composant chargé de l'affichage de la liste doit être mis à jour dans l'effet. On aura donc la liste elle-même comme première variable et le filtre de recherche comme deuxième variable.

```tsx
useEffect(() => {
  //Mise à jour du composant qui affiche les items de la liste
}, [theList, searchFilter]);
```

Évidemment, si on veut programmer des effets différents dans l'application, on en rajoute d'autres :

```tsx
useEffect(() => {
  //Un effet
}, [maVariable1]);

useEffect(() => {
  //Un autre effet différent du premier
}, [maVariable2]);
```

Avoir 2 effets différents sur la même variable est possible, mais à condition que la logique de l'effet soit **complètement différente**. Par exemple une mise à jour d'un composant dans le premier effet et une journalisation (log) dans un 2e effet.

### Cas 3 : Sans tableau de dépendances

```tsx
useEffect(() => {
  console.log("Ce code s'exécute à chaque RENDU");
});
```

- Le code s'exécute après chaque rendu du composant, peu importe la cause.
- Rarement utilisé car cela peut entraîner des effets secondaires ou des performances médiocres.

---

## Use effect avec retour de fonction

`useEffect` **peut retourner une fonction**, cette fonction sera appelée dans 2 situations :

- Quand le composant est détruit (démonté)
- Quand les dépendances changent

Voici un exemple de `useEffect` avec retour de fonction et la trace de l'exécution :

```tsx
useEffect(() => {
  console.log("Effet lancé");

  return () => {
    console.log("Cleanup effectué");
  };
}, [count]);
```

```bash
Effet lancé (count=0)
→ count devient 1
Cleanup effectué (pour count=0)
Effet lancé (count=1)
→ count devient 2
Cleanup effectué (pour count=1)
Effet lancé (count=2)
```

Les exemples montrés plus haut ne nécessitaient pas de return. Par contre, considérons cet effet qui démarre un timer au premier rendu :

```tsx
useEffect(() => {
  const interval = setInterval(() => {
    console.log('tic');
  }, 1000);

  return () => {
    clearInterval(interval);
    console.log('nettoyage');
  };
  //tableau vide -> premier rendu seulement
}, []);
```

Dans ce cas précis, un nettoyage de "l'effet précédent" est de mise afin d'éviter que le timer reste en mémoire au démontage du composant.

---

## En résumé

| Syntaxe                    | Fréquence d'exécution                                   |
| -------------------------- | ------------------------------------------------------- |
| `useEffect(() => {})`      | À chaque rendu                                          |
| `useEffect(() => {}, [])`  | Une seule fois après le premier rendu                   |
| `useEffect(() => {}, [x])` | Après le premier rendu, puis à chaque changement de `x` |

Si un nettoyage en mémoire est requis (ex : timer, abonnement à un événement, etc.), effectuer le nettoyage dans une fonction de retour.


