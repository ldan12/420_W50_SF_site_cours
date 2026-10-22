---
id: arduino
title: Arduino
sidebar_label: Arduino
---


# Introduction à l'Arduino, à PlatformIO et aux notions d’électricité pour l’IoT

## Qu’est-ce qu’un microcontrôleur ?

Un **microcontrôleur** est un petit ordinateur intégré dans un circuit électronique.  
Il contient :

- Un **processeur (CPU)** : exécute les instructions.  
- De la **mémoire Flash** : stocke le programme (firmware).  
- De la **RAM** : conserve les variables temporaires.  
- Des **ports d’entrée/sortie (I/O)** : communiquent avec le monde extérieur.

Contrairement à un ordinateur, il **n’exécute qu’un seul programme** à la fois.  
Ce programme démarre automatiquement dès que la carte reçoit de l’énergie.


## La carte de développement (Arduino / ESP32)

### Composants principaux

| Élément | Description |
|----------|-------------|
| **Microcontrôleur (ATmega328 / ESP32)** | Cerveau de la carte |
| **Port USB** | Permet la programmation et l’alimentation |
| **Régulateur de tension (5V ou 3.3V)** | Maintient une tension stable |
| **Broches numériques (D0–D13)** | Lecture/écriture de signaux logiques |
| **Broches analogiques (A0–A5)** | Lecture de signaux variables |
| **Broches GND (masse)** | Point de référence du circuit |
| **Bouton Reset** | Redémarre le programme |
| **LED intégrée (souvent D13)** | Indicateur d’activité |

Les **broches GND** sont présentes à plusieurs endroits sur la carte.  
Elles représentent **le 0 volt**, **la référence commune** de tout le circuit.

---

## Structure et exécution du programme (firmware)

Voici le code de départ dans PlatformIO :

```cpp
#include <Arduino.h>

// put function declarations here:
int myFunction(int, int);

void setup() {
  // put your setup code here, to run once:
  int result = myFunction(2, 3);
}

void loop() {
  // put your main code here, to run repeatedly:
}

// put function definitions here:
int myFunction(int x, int y) {
  return x + y;
}
```
### Cycle d’exécution

1. Compilation sur l’ordinateur
  - PlatformIO compile le code C++ en binaire machine (.bin).
  - Ce fichier est envoyé à la carte via le bootloader.

2. Démarrage du microcontrôleur
  - Dès qu’on alimente la carte, le programme démarre.
  - La fonction setup() est exécutée une seule fois au démarrage.
  - Ensuite, la fonction loop() tourne en boucle infinie, sans interruption.

3. Interaction avec le matériel
  - Le code manipule les broches pour lire (entrée) ou agir (sortie).
  - C’est ce lien entre le code logiciel et le monde physique qui fait l’IoT.


Exemple dans le code de départ:

  - setup() appelle myFunction(2, 3) une fois.
  - loop() est vide, donc la carte ne fait rien après l’initialisation.
  - Le microcontrôleur reste sous tension et attend les prochaines instructions.

## Notions d’électricité essentielles

### La loi d’Ohm

**U = R × I**

| Symbole | Unité      | Description                         |
| ------- | ---------- | ----------------------------------- |
| U       | Volt (V)   | La "pression" électrique            |
| I       | Ampère (A) | Le flux d’électrons                 |
| R       | Ohm (Ω)    | La résistance au passage du courant |


Analogie :
  - Tension = pression de l’eau.
  - Courant = débit d’eau.
  - Résistance = étroitesse du tuyau.

### Le rôle du GND (masse)

Le GND (Ground) est le point de référence de tout le circuit.
C’est là que le courant retourne après avoir traversé les composants.

Exemple :

Si on alimente une LED avec la broche D13,
le courant sort de D13 → traverse la LED → passe dans la résistance → retourne au GND.
Ce trajet complet forme une boucle fermée : sans GND, le courant ne circule pas.

**À ne jamais faire : court-circuiter le GND et le +5V**

Un court-circuit se produit lorsque le courant trouve un chemin direct entre le +5V et le GND sans résistance entre les deux.

Résultat :

  - Le courant devient extrêmement fort.
  - Le régulateur ou la carte surchauffe.
  - Dans le meilleur des cas : la carte redémarre.
  - Dans le pire des cas : le microcontrôleur est grillé.

**Toujours vérifier les connexions avant d’alimenter le circuit !**

Tension selon la carte
| Carte           | Tension logique | Tension d’alimentation typique |
| --------------- | --------------- | ------------------------------ |
| Arduino Uno     | 5V              | USB ou Jack 9V                 |
| ESP32 / ESP8266 | 3.3V            | USB uniquement                 |


**Envoyer 5V sur une broche 3.3V détruit l’entrée du microcontrôleur !**

## Premier exemple - LED

Pour connecter une LED :

  - On place l’anode (+) sur la broche D13.
  - On place la cathode (-) vers une résistance de 220 Ω.
  - On place l’autre extrémité de la résistance vers GND.

Voici le programme :

```cpp
#include <Arduino.h>

void setup() {
  pinMode(13, OUTPUT);
}

void loop() {
  digitalWrite(13, HIGH);
  delay(500);
  digitalWrite(13, LOW);
  delay(500);
}
```

Que se passe-t-il ici ?

  - digitalWrite(13, HIGH) : envoie 5V sur la broche D13.
  - Le courant passe dans la LED → la résistance → le GND.  
  - La LED s’allume
  - Puis LOW coupe le courant → LED s’éteint.
  - Ce processus s'effectue en boucle avec délai d'une demi seconde entre chaque passe


→ Sans connexion au GND, la LED ne s’allumerait jamais.
→ Et si on reliait accidentellement D13 directement à GND sans résistance, on raut court-circuit immédiat.

## Capteurs et actionneurs

| Type           | Exemple                      | Direction | Rôle                    |
| -------------- | ---------------------------- | --------- | ----------------------- |
| **Capteur**    | Température, bouton, lumière | Entrée    | Fournit une information |
| **Actionneur** | LED, moteur, relais          | Sortie    | Exécute une action      |

Le GND sert aussi à référencer tous les capteurs.

En d'autres mots, si le capteur n’a pas le même GND que la carte, les lectures seront fausses, instables ou impossibles.

## Cycle de développement complet

| Étape               | Élément impliqué        | Description                                 |
| ------------------- | ----------------------- | ------------------------------------------- |
| 1. Écriture du code | Ordinateur + PlatformIO | Programmation du firmware                   |
| 2. Compilation      | Toolchain AVR/ESP       | Conversion en langage machine               |
| 3. Téléversement    | USB Bootloader          | Transfert sur la carte                      |
| 4. Exécution        | Microcontrôleur         | `setup()` puis `loop()`                     |
| 5. Interaction      | Broches + GND           | Communication avec les capteurs/actionneurs |


Toujours penser en termes de boucle électrique :

une sortie → un composant → le GND.

## Sécurité et bonnes pratiques

**Toujours :**

  - Relier tous les GND entre les composants.
  - Vérifier les connexions avant d’alimenter.
  - Utiliser des résistances pour limiter le courant.
  - Débrancher la carte avant de modifier le montage (branchement des composantes).

**Ne jamais :**

- Connecter directement une sortie (ex. D13) à GND.
- Relier le 5V au 3.3V.
- Alimenter un composant à une tension supérieure à celle prévue.

