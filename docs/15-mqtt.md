---
id: mqtt
title: MQTT
sidebar_label: MQTT
---


# MQTT

MQTT (Message Queuing Telemetry Transport) est un protocole de messagerie léger où les appareils publient des messages sur des sujets (*topics*) et d'autres s'y abonnent, par l'intermédiaire d'un serveur central appelé *broker*. Il est conçu pour les objets connectés, comme l'ESP32 utilisé dans le cours.

## Pourquoi MQTT?

Sur un microcontrôleur, chaque octet et chaque connexion comptent. MQTT garde une connexion TCP ouverte et envoie de très petits messages, alors qu'HTTP envoie des en-têtes volumineux à chaque requête.


## Architecture : *broker*, éditeurs et abonnés

Les clients ne se parlent jamais directement. Le broker reçoit chaque message et le redistribue à tous les clients abonnés au bon topic. L'éditeur (*publisher*) ignore qui reçoit ses messages, et l'abonné (*subscriber*) ignore qui les a émis. Un même client peut être les deux à la fois.

Ce découplage évite de connaître l'adresse IP des autres appareils : il suffit de connaître l'adresse du *broker* et le nom du *topic*.

## Les *topics*

Un *topic* est une chaîne hiérarchique dont les niveaux sont séparés par des barres obliques, par exemple `maison/salon/temperature`. Il n'est pas créé à l'avance : il existe dès qu'un client publie ou s'abonne dessus.
- Les *topics* sont sensibles à la casse : Salon et salon sont deux *topics* différents.
- Éviter les espaces et les accents, et ne pas commencer par /.
- Un *topic* qui commence par $ est réservé au *broker*.
- Nommer du général au particulier, et séparer l'état de la commande : `cours/esp32/NOM-ETUDIANT/mouvement` et `cours/esp32/NOM-ETUDIANT/commande`.
Les jokers s'utilisent seulement dans un abonnement, jamais pour publier.


| Joker |	Signification |	Exemple d'abonnement | Reçoit |
|----------|-------------|---------------------|--------|
| +	| Un seul niveau | cours/esp32/+/mouvement |	cours/esp32/karine/mouvement <br /> cours/esp32/marc/mouvement |
| # |	Tous les niveaux restants (à la fin seulement) |	cours/esp32/# |	Tout ce qui est sous cours/esp32/ |



## Qualité de service (QoS)

Le QoS précise la garantie de livraison d'un message entre deux maillons (client vers broker, puis broker vers abonné).

| QoS |	Nom                	| Garantie |	Coût |	Usage typique |
|-----|---------------------|----------|-------|----------------|
| 0	  | Au plus une fois    |	Aucune : le message peut se perdre |	Le plus léger	| Mesures fréquentes, perte sans conséquence |
| 1 	| Au moins une fois	  | Livré, doublons possibles |	Une confirmation (PUBACK) |	Événements importants |
| 2   |	Exactement une fois	| Livré une seule fois |	Quatre messages d'échange |	Commandes critiques |


La bibliothèque `PubSubClient` publie uniquement en QoS 0 et s'abonne en QoS 0 ou 1. Dans l'exercice 8, un message peut donc être perdu si le réseau est instable.

## Choisir un *broker*

| Broker	| Type |	Remarque |
|---------|------|-----------|
| Mosquitto (Eclipse) |	Libre, à installer soi-même (serveur, Raspberry Pi) |	Référence, très léger |
| HiveMQ public <br /> (broker.hivemq.com) |	Public et gratuit	| Pour les essais seulement <br /> aucune authentification |
| EMQX, HiveMQ Cloud	| Services infonuagiques | Plus de fonctions, création de compte |

Pour une page web, le navigateur ne peut pas ouvrir de connexion MQTT directe : il utilise MQTT sur WebSocket, sur un port différent selon le *broker* (à vérifier dans sa documentation).

Un *broker* public comme `broker.hivemq.com` n'offre aucune confidentialité : tout le monde peut lire et écrire sur n'importe quel *topic*. Ne jamais y publier d'informations personnelles.



## MQTT avec l'ESP32 (`PubSubClient`)

Un programme MQTT suit toujours les mêmes étapes : connexion Wi-Fi, déclaration du *broker*, connexion MQTT, publication ou abonnement, puis appel régulier de `loop()`.

| Fonction |	Rôle |
|----------|-------|
| `setServer(broker, port)`	| Déclare l'adresse du broker |
| `connect(id)`	| Ouvre la session MQTT |
| `connected() / state()`	| Vérifie la connexion / donne le code d'erreur |
| `publish(topic, message)`	| Publie un message |
| `subscribe(topic)`	| S'abonne à un *topic* |
| `setCallback(fonction)` |	Fonction appelée à chaque message reçu |
| `loop()` |	Maintient la connexion et reçoit les messages (à appeler souvent) |

## Exemple : recevoir une commande pour la DEL

```cpp
const char* TOPIC_CMD = "cours/esp32/NOM-ETUDIANT/commande";

// Appelée à chaque message reçu sur un topic auquel on est abonné
void quandMessage(char* topic, byte* donnees, unsigned int longueur) {
  String texte;
  for (unsigned int i = 0; i < longueur; i++) texte += (char)donnees[i];

  if (texte == "ON")  digitalWrite(2, HIGH);
  if (texte == "OFF") digitalWrite(2, LOW);
}

// Dans setup() : mqtt.setCallback(quandMessage);
// Après chaque connexion MQTT réussie : mqtt.subscribe(TOPIC_CMD);
```

### Points d'attention :
- Refaire `subscribe()` après chaque reconnexion, car les abonnements sont perdus.
- Garder la fonction de rappel courte, sans `delay()`.
- Le tampon de `PubSubClient` est de 256 octets par défaut : un message plus long est ignoré, sauf si on appelle `mqtt.setBufferSize()`.


## Tester sans écrire de code

Utiliser Mosquitto en ligne de commande :
`mosquitto_sub -h broker.hivemq.com -t "cours/esp32/+/mouvement"`

On peut télécharger [Mosquitto](https://mosquitto.org/download/).
