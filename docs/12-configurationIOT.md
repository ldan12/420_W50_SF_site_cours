---
id: configiot
title: Configuration IoT
sidebar_label: Configuration IoT
---

# Configuration de l'environnement Iot (Internet Of Things)

## Extension PlatformIO

### Installation

Tout comme la partie mobile du cours, nous allons effectuer le développement Iot dans **VSCode**

Pour ce faire, installez l'extension **PlatformIO**

![PlatformIO](/img/platformio.jpg)

### Connexion du micro-contrôleur

**IMPORTANT**

Le micro-contrôleur que vous allez manipuler est très sensible à l'électricité statique. Avant de le retirer de son sachet, trouvez un morceau de métal relié au sol proche de vous (ex: cadre de porte métallique) et touchez-le avec votre doigt, ceci neutralisera votre électricité statique accumulée. Portez une attention particulière à cette consigne si vous venez de marcher sur du tapis ou portez des vêtements synthétiques.

Sortez le micro de son sachet avec précaution.

- Branchez le micro-contrôleur à votre ordinateur à l'aide du connecteur USB.
- Ouvrez le gestionnaire de périphériques (device manager) sur votre poste de travail.
- Vérifiez que le micro-contrôleur apparaît dans la section des ports COM et LPT, avec un port COM d'attribué :

![Com Port](/img/comport.jpg)

Si cela fonctionne, prenez note le # du port COM qui vous a été attribué et continuez à la section **Création d'un projet PlatformIO**.

**Si ce n'est pas le cas et que le micro-contrôleur n'est pas reconnu** :

![Error usb](/img/errorusb.png)

Dans ce cas, il va falloir installer le pilote du micro-contrôleur.

Allez sur cette page : https://www.silabs.com/software-and-tools/usb-to-uart-bridge-vcp-drivers?tab=downloads

Et téléchargez le pilote nommée **CP210x Universal Windows Driver**, placez le .zip téléchargé n'importe où sur votre poste de travail, pas besoin de l'extraire.

Retournez dans le gestionnaire de périphériques et cliquez-droit sur le périphérique qui apparaît en erreur :

- Choisissez l'option d'installer le pilote
- Choisissez l'option de parcourir votre poste de travail
- Spécifiez le fichier .zip téléchargé en guise de pilote et procédez à l'installation

Si l'opération fonctionne, le micro-contrôleur sera reconnu et déplacé dans la section des ports COM et LPT de votre gestionnaire de périphériques, prenez en note le # du port COM attribué.

### Création d'un projet PlatformIO

En cliquant sur l'icône de l'extension installée auparavant dans votre menu **VSCode**, on accède au menu qui permet de créer un nouveau projet: 

![PlatformIO_NewProject](/img/platformio_newproject.jpg)

![PlatformIO_NewProject_click](/img/platformio_newproject_click.jpg)

Nommez votre projet et sélectionnez le type de micro-contrôleur indiqué ci-dessous, le cadriciel (Framework) devrait s'auto compléter ensuite à **Arduino**

Afin de vous retrouver, il est aussi recommandé de décocher l'emplacement par défaut et de choisir vous même votre dossier de classification :

![PlatformIO_NewProject](/img/platformio_firstproject.jpg)

Terminez la création du projet, ceci prendra quelques secondes.