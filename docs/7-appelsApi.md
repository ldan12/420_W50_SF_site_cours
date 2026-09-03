---
id: api
title: Appels d'API
sidebar_label: Appels d'API
---

# Introduction aux appels API dans React Native

## Structure du dossier API

Pour organiser proprement les appels API et les types, voici une structure recommandée avec en exemple la mise en place d'un API de _students_ :

```
/api
├── student             --> Dossier thématique pour l'API des étudiants (si on a plusieurs routes de base à gérer)
│ └── studentApi.ts     --> Fonctions spécifiques aux appels liés aux étudiants
/mappers
├── student             --> Dossier thématique pour le mappage des étudiants
│ └── studentMapper.ts  --> Fonctions de mappage liées aux étudiants
/types
├── student.ts          --> Types reliés au student
```

## 1. Définition des types

La première étape est **d'impérativement** bien comprendre le format des données dans les appels d'API qui nous intéressent, ce qui nous permet de construire l'interface de réponse avec la bonne sructure :

Dans un fichier **student.ts** dans le dossier `types` (au niveau de l'application) :

```ts
//Format de la réponse retourné par l'API
export interface StudentListResponse {
  students: {
    id: string;
    name: string;
    email: string;
    enrolled: boolean;
  }[];
  totalCount: number;
}

//autres interfaces de réponse selon l'appel
//...

//Représentation d'un student dans notre application, si elle diffère de celle de l'API
export interface Student {
  id: string;
  fullName: string;
  emailAddress: string;
  isEnrolled: boolean;
}
```

**Note** : On pourrait aussi déclarer un dossier `types` dans le dossier `student` de l'api pour isoler le type de réponse.

## 2. Appel à l'API

Les appels sont regroupés dans le fichier `studentApi.ts`, ce sont des appels asynchrones (**await**) qui livrent type de réponse précis (**Promise**). Il ne faut pas oublier la gestion d'exception :

```ts
import { StudentListResponse } from "@/types/student";

const BASE_URL = //racine de l'API;
  async function getStudents(): Promise<StudentListResponse[]> {
    const response = await fetch(BASE_URL + "/students");
    if (response.ok) {
      const json = await response.json();
      return json as StudentListResponse[];
    } else {
      const error = await response.text();
      throw new Error(error);
    }
  };
```

L'exemple précédent fait en sorte que la réponse est renvoyée directement à l'application, ce qui peut être très utile pour gérer la **pagination** et le **total** d'éléments reçus. Cette approche impose par contre un **couplage important** entre l'application et l'API ainsi qu'une bonne compréhension de cette dernière au niveau applicatif.

Il est aussi possible de transiger l'objet métier (`Student`), ceci nécessite un mappage au niveau de l'API. Notez la **Promise** de la méthode qui a changé pour `Student`, le mappage et l'appel à l'API qui continue évidemment d'attendre sa réponse. Notez ici l'utilisation de `studentMapper` défini dans la hiérarchie de l'application :

```ts
//studentMapper.ts
export const mapStudentListResponseToStudents = (
  response: StudentListResponse[]
): Student[] => {
  return response.students.map((s) => ({
    id: s.id,
    fullName: s.name,
    emailAddress: s.email,
    isEnrolled: s.enrolled,
  }));
};

//Autres mappages, ex Student vers ApiStudent dans le cas d'un envoi (post)
//...
```

```ts
import { StudentListResponse } from "@/types/student";
import { mapStudentListResponseToStudents } from "@/mappers/student/studentMapper";

const BASE_URL = //racine de l'API;

export default async function getStudents() : Promise<Student[]>{

    const response = await fetch(BASE_URL + "/students");

    if (!response.ok) {
        throw new Error("Erreur lors de l’appel à l’API");
    }

    const result : StudentListResponse[] = await response.json();
    return mapStudentListResponseToStudents(result);
}
```

L'exemple précédent réduit le couplage entre l'application et l'API (géré dans le mapper), mais nous fait perdre les métadonnées relatives à la pagination et au total d'éléments.

Le développeur a donc la reponsabilité de choisir la bonne approche selon le contexte applicatif. Certains vont opter pour l'implantation de 2 appels distincts, subvenant aux 2 situations.

## 3. Appel de l'API dans l'application

Un usage judicieux du `useEffect` et du `useState` rend facile l'appel à un API. Attention de mettre en place une logique asynchrone pour la récupération! Comme useEffect est synchrone, il faut utiliser `then`, pas `await` :

```ts
const [students, setStudents] = useState<Student[]>([]);

//dans le useEffect, appelle une méthode de fetching qui ira chercher les étudiants :
useEffect(() => {
  getStudents().then((data) => {
    setStudents(data);
  });
}, []);
```

L'ajout d'un état de chargement sera à préconiser pour les appels d'API, car un indicateur de chargement est souvent de mise dans ce genre de situation :

```ts
const [loading, setLoading] = useState<boolean>(false);
const [students, setStudents] = useState<Student[]>([]);

//On gérera l'état de chargement dans la logique de récupération en n'oubliant pas de le terminer en cas d'erreur !
```
