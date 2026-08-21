---
id: installation-react
title: Installation React Native
sidebar_label: Installation React Native
---

# Configuration de l'environnement mobile

Dans le cadre de ce cours, nous développerons des application mobiles en `React native`.

Voici les logiciels à installer pour cette partie du cours. Il est à noter que vous avez sûrement la majorité de ces logiciels d'installés de par les sessions antérieures. Assurez-vous que, lorsque demandé, votre version est la bonne.

## Node.js

Vérifiez tout d'abord si vous avez `Node.js` d'installé :

```powershell title="powershell (ou cmd)"
node -v
```

Au moment d'écrire ces lignes, `Node.js` est à la version **26**

Si votre version date un peu trop (18 ou moins), il est fortement recommandé de désinstaller `Node.js` depuis vos programmes et de procéder à une nouvelle installation :

https://nodejs.org/en

## Android Studio

Assurez-vous ensuite que vous avez `Android Studio`. Sinon, installez la dernière version :

https://developer.android.com/studio

## VS Code

`VS Code` sera notre éditeur pour ce cours :

https://code.visualstudio.com/

## Test de l'installation

Dans le but de tester l'installation, nous allons créer une application mobile de base et l'exécuter dans un émulateur Android.

### Étape 1 : Démarrer l'émulateur

Démarrez `Android Studio` et lancez le `Device Manager` :

![Démarrage Device manager](/img/studio-start.jpg)

Si vous avez procédé à une installation neuve de `Android Studio`, vous aurez un émulateur par défaut (voir image ci-dessous). Si vous avez un émulateur d'une ancienne session, vous pouvez également le choisir. Dans tous les cas, démarrez un émulateur fonctionnel :

![Démarrage Émulateur](/img/device-start.jpg)

### Étape 2 : Créer l'application mobile avec Expo

Créez-vous un dossier de travail pour le cours (ex : 420-W50-SF/Mobile/Travaux) et ouvrez ce dossier dans `VS Code`.

Toujours dans `VS Code`, ouvrez un terminal (View/Terminal) qui sera automatiquement placé sur votre dossier de travail.

Dans le cadre de ce cours, nous allons utiliser `Expo`, une plateforme open source qui accélère le développement et le déploiement d'application applications mobiles en `React native` sur l'émulateur Android.

Pour créer une application mobile (nommée firstApp) avec `Expo` en `React native`, inscrivez ceci dans le terminal de `VS Code`:

```powershell title="terminal VS Code"
npx create-expo-app firstApp
```
Vous devrez choisir le SDK de votre application: **vous devez choisir *For learning with Expo Go (SDK 54)*** car la version SDK 57 n'est pas encore supportée par Expo Go (si vous désirez tester sur votre téléphone).

On vous demandera surement d'installer le package `Expo`, répondez oui.

L'application créée apparaîtra dans votre dossier `VS Code` car elle a été créée dans votre répertoire de travail. Changez le répertoire courant afin d'être dans votre nouvelle application :

```powershell title="terminal VS Code"
cd firstApp
```

### Étape 3 : Lancer l'application mobile dans l'émulateur en exécution

Votre émulateur étant lancé (étape 1), `Expo` va être en mesure de le reconnaître et d'y lancer votre application en inscrivant ceci dans le terminal `VS Code` :

```powershell title="terminal VS Code"
npx expo start
```

Patientez queqlues instants, quelques options vous seront alors présentées :

![options Expo](/img/expo-options.jpg)

**Choisissez l'option `a`** pour l'émulateur Android.

Dirigez vous dans l'émulateur Android où l'application démarrera et sera "buildée", patientez quelques instants. Au terme de ce processus, vous y verrez votre première application React native :

![Première application](/img/firstapp.jpg)

Si tel est le cas, votre installation est fonctionnelle et vous être prêt(e)s pour le cours !
