---
id: firestore
title: Stockage sur Firestore
sidebar_label: Stockage sur Firestore
---

# Introduction à Firestore

## Qu'est-ce que Firestore ?

Firestore, aussi appelé Cloud Firestore, est une base de données NoSQL proposée par Firebase, la plateforme mobile de Google.

| Caractéristique            | Détail                                                                          |
|----------------------------|---------------------------------------------------------------------------------|
| **Type**                   | Base de données NoSQL (documents / collections)                                 |
| **Temps réel**             | Oui — les données peuvent être synchronisées automatiquement entre utilisateurs |
| **Hébergée dans le cloud** | Oui — tu n'as pas besoin de gérer un serveur ou une base de données             |
| **Scalabilité**            | Conçue pour gérer des millions d’utilisateurs                                   |
| **Multi-plateforme**       | Android, iOS, Web, React Native, Node.js, etc.                                  |
| **Sécurité**               | Règles de sécurité Firebase pour gérer l'accès par utilisateur ou rôle          |


## Création de la base de données

Avant toute chose, il faut provisionner une base de données sur **Firebase**

Accédez au menu Créer/Firestore Database depuis le tableau de bord de votre projet :

![Firestore](/img/firestore.png)

Créez ensuite une base de données :

![Firestore DB](/img/firestoredb.png)

Suivez les étapes jusqu'à l'option de démarrage pour laquelle il faudra choisir l'option suivante :

![irestore DB test](/img/firestoredbtest.png)

Lorsque fait, votre base de données NoSQL sera créée.

**Note** : Vous n'avez pas à pré-créer vos collections (tables), ce sera fait lors de votre premier appel de stockage.

## Chargement de Firestore

**Firestore** est lié à la configuration **Firebase** de l'application (fichier `firebaseConfig.ts`). Afin de charger **Firestore**, il suffit de le rendre disponible de la façon suivante dans le fichier de configuration :

```tsx
//importer le package :
import { getFirestore } from 'firebase/firestore'

const firebaseConfig = {
  //Votre config Firebase
}

const app = //chargement de l'app

export const auth = //initialisation de l'authentification

//la base de données utilisable dans l'application:
export const db = getFirestore(app);
```

## Écriture et lecture de données

### Écriture 
La méthode clé d'écriture est le `setDoc` :

https://firebase.google.com/docs/firestore/manage-data/add-data?hl=fr#set_a_document

On remarque la spécification de la "clé primaire" de l'enregistrement ("LA" dans l'exemple ci-haut).

### Lecture

Les 2 méthodes clés de lecture **Firestore** sont le `getDoc` (un seul enregisrement) et le `getDocs` (plusieurs enregistrements)

Dans le cas du `getDoc`, on effectue la requête sur la clé primaire de l'enregistrement afin de récupérer un seul enregistrement :

https://firebase.google.com/docs/firestore/query-data/get-data?hl=fr#get_a_document


Dans le cas du `getDocs` on utilise plutôt une clause **where** qui retourne un ensemble d'enregistrements :

https://firebase.google.com/docs/firestore/query-data/get-data?hl=fr#get_multiple_documents_from_a_collection

 
## Utilisation de Firestore dans l'application

Pour effectuer une requête dans un composant, il s'agit de charger la base de données de la config, les méthode **Firestore** et de suivre la documentation pas à pas :

```tsx
//importer le package :
import { db } from '../firebaseConfig'
import { setDoc } from 'firebase/firestore'

//Voir la doc pour les requêtes
```






