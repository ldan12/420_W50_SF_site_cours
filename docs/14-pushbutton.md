---
id: pushbutton
title: Push Button
sidebar_label: Push Button
---



# Le *Push Button* (Bouton-poussoir) — Présentation complète

## 1. Qu’est-ce qu’un *push button* ?

Un **push button** est un **interrupteur momentané** :  
- Quand on appuie dessus → le circuit se ferme → le courant passe.  
- Quand on relâche → le circuit s’ouvre → le courant ne passe plus.  

Il permet à un microcontrôleur (comme un Arduino ou ESP32) de **détecter une action utilisateur** (clic, validation, contrôle de séquence, etc.).

---

## 2. Branchement typique

Le bouton possède **4 pattes**, mais elles sont en réalité **connectées 2 à 2** (les côtés opposés sont reliés entre eux).

### La résistance de rappel (pull-up et pull-down)

Une entrée numérique (GPIO) doit être reliée à une tension connue pour que le microcontrôleur puisse déterminer son état (HIGH ou LOW).

Lorsqu'un bouton-poussoir est relâché, le circuit est ouvert. Si la pin n'est reliée ni au 3,3 V ni au GND, elle se retrouve dans un état flottant. Elle peut alors retourner aléatoirement HIGH ou LOW.

Pour éviter ce problème, on utilise une résistance de rappel, qui permet de maintenir la pin à un état électrique connu lorsque le bouton n'est pas appuyé.

Il existe deux configurations :

Pull-down : la résistance relie la pin au GND. Au repos, la pin lit LOW (0 V). Lorsqu'on appuie sur le bouton, elle reçoit 3,3 V et lit HIGH.

Pull-up : la résistance relie la pin au 3,3 V. Au repos, la pin lit HIGH. Lorsqu'on appuie sur le bouton, elle est reliée au GND et lit LOW.

### Variante plus simple : utiliser la résistance interne de l’Arduino

```cpp
pinMode(2, INPUT_PULLUP);
```

Dans ce cas :
- Le bouton relie la **broche au GND** quand on appuie.  
- Attention, c'est le contraire ici ! L’état est **LOW quand appuyé**, **HIGH quand relâché**.

---

## 3. Le concept de *debouncing* (anti-rebond)

Quand on appuie sur un bouton, le contact mécanique **rebondit** :  
- Le signal n’est pas net : il passe de 0 à 1 plusieurs fois pendant quelques millisecondes.  
- Sans gestion du *debouncing*, ton programme pourrait détecter **plusieurs appuis** pour un seul clic.  

### Solution :

```cpp
if (digitalRead(buttonPin) == LOW) {
    delay(50);

    if (digitalRead(buttonPin) == LOW) {
        Serial.println("Bouton appuyé !");

        // Optionnel : Attendre que le bouton soit relâché si on en a besoin
        while (digitalRead(buttonPin) == LOW) {
            delay(10);
        }

        delay(50); // Optionnel si relâchement géré : Anti-rebond au relâchement
    }
}
```

---

## 4. Exemple : allumer une LED pendant 500 ms

### Matériel :
- 1 bouton-poussoir  
- 1 LED + résistance 220 Ω  
- Fils et breadboard  
- Arduino  

### Branchements :
- Bouton entre **pin 2** et **GND**  
- LED sur **pin 13** (ou une autre pin numérique)  
- Utilisation du `INPUT_PULLUP` pour simplifier le montage.

### Code :

```cpp
const int buttonPin = 2;
const int ledPin = 13;

void setup() {
  pinMode(buttonPin, INPUT_PULLUP); // résistance interne activée
  pinMode(ledPin, OUTPUT);
}

void loop() {
  if (digitalRead(buttonPin) == LOW) {
    delay(50);

    //Appui confirmé
    if (digitalRead(buttonPin) == LOW) {
      digitalWrite(ledPin, HIGH); // allume la LED
      delay(500);                 // garde la LED allumée 500ms
      digitalWrite(ledPin, LOW);  // éteint la LED
    }

    // Ici c'est utile d'attendre le relâchement afin de ne pas redéclencher un allumage
    while (digitalRead(buttonPin) == LOW) {
        delay(10);
    }

    delay(50); // Anti-rebond au relâchement
  }
}
```

---

## 5. *Pull-down* externe vs *Pull-up* interne

| Type de résistance | Câblage du bouton | Lecture quand **appuyé** | Lecture quand **relâché** |
|--------------------|------------------:|---------------------------|-----------------------------|
| **Pull-down externe** | Bouton entre **+5V** et la pin | `HIGH` | `LOW` |
| **Pull-up interne (`INPUT_PULLUP`)** | Bouton entre **GND** et la pin | `LOW` | `HIGH` |

### Résumé

- **Pull-down externe** → nécessite une résistance physique entre la pin et GND.  
- **Pull-up interne** → utilise la résistance intégrée de l’Arduino pour relier la pin à +5V.  

Les deux méthodes donnent le même résultat logique, seul le **sens du signal** est inversé.  

---

## 6. Interprétation logique dans le code

Même si l’état électrique est inversé avec `INPUT_PULLUP`, tu peux l’interpréter naturellement :

```cpp
int buttonState = digitalRead(buttonPin);
bool pressed = (buttonState == LOW); // bouton appuyé

if (pressed) {
  // action à effectuer lors de l'appui
}
```

---

## 7. Front descendant

Un front descendant (ou front négatif) correspond au moment précis où un signal passe de l’état HAUT à l’état BAS. En d’autres mots : le signal “descend” de 1 (HIGH) vers 0 (LOW).

| Type de front | Transition | Signification courante |
|---------------|------------|------------------------|
| **Front descendant** | HIGH → LOW | Début d’un appui (si INPUT_PULLUP) |
| **Front montant** | LOW → HIGH | Relâchement du bouton |


## En résumé

- Le **push button** est un interrupteur momentané.  
- Le **pull-down** garde la pin à LOW par défaut (appui = HIGH).  
- Le **pull-up** garde la pin à HIGH par défaut (appui = LOW).  
- Le **debouncing** évite les faux appuis.  

---