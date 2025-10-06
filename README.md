# LAB-1/08 : Mise en Place d'une Veille en Cybersécurité Automatisée

Ce laboratoire explique comment construire un système de veille technologique et de sécurité (threat intelligence) proactif et automatisé. La méthode combine plusieurs outils pour centraliser l'information, générer des alertes en temps réel et partager les découvertes pertinentes avec une équipe.

Les étapes clés incluent :

* La centralisation des sources d'information via des flux RSS à l'aide d'un agrégateur comme **Feedly**.
* L'automatisation des alertes pour les informations critiques en créant une règle ("Applet") sur la plateforme **IFTTT** (If This Then That).
* L'intégration d'un flux RSS directement dans un canal de communication d'équipe, ici **Microsoft Teams**, pour une diffusion large et une collaboration efficace.

## 🎯 Objectif

L'objectif est de transformer une veille manuelle et chronophage en un processus automatisé et efficient. Ce système doit permettre de surveiller en continu des sources de confiance, d'être alerté immédiatement sur les menaces les plus critiques (via email) et de partager facilement les actualités importantes avec les collaborateurs concernés.

<img width="1024" height="682" alt="image" src="https://github.com/user-attachments/assets/c17a2e16-2a9f-42a3-b155-1cc0439e9c63" />


## 🛠️ Prérequis

* Un compte **Feedly** (version gratuite suffisante).
* Un compte **IFTTT** (version gratuite suffisante).
* Un accès à **Microsoft Teams** (ou un autre outil collaboratif supportant les connecteurs RSS).
* Une liste de sources de veille fiables disposant de flux RSS (ex: CERT-FR, ANSSI, The Hacker News, etc.).

## ⚙️ Étapes de mise en place

### 1. Centralisation des Flux avec Feedly

Feedly est utilisé pour agréger tous les articles de nos sources en un seul endroit.

* **a. Création d'un dossier de veille**
    Dans Feedly, créez un nouveau dossier (Feed) nommé `Veille Cyber` pour organiser les sources.

* **b. Ajout des sources RSS**
    Recherchez et ajoutez les sites web ou les URL de flux RSS des sources de confiance.
    Pour ce lab, mes sources incluent :
    * `cert.ssi.gouv.fr`
    * `cnil.fr`
    * `cybermalveillance.gouv.fr`
    * `thehackernews.com`
    * `darkreading.com`
    * `krebsonsecurity.com`

### 2. Automatisation des Alertes Critiques avec IFTTT

IFTTT va nous permettre de recevoir un email dès qu'une nouvelle alerte est publiée par une source critique comme le CERT-FR.

* **a. Création de l'Applet : Le Déclencheur (If This)**
    1.  Créez un nouvel Applet et pour le déclencheur "If This", sélectionnez le service **RSS Feed**.
    2.  Choisissez le trigger `New feed item`.
    3.  Renseignez l'URL du flux RSS du CERT-FR : `https://www.cert.ssi.gouv.fr/feed/`.

* **b. Création de l'Applet : L'Action (Then That)**
    1.  Pour l'action "Then That", sélectionnez le service **Email** ou **Gmail**.
    2.  Choisissez l'action `Send an email`.
    3.  Connectez votre compte email et configurez le destinataire (vous-même ou une liste de distribution d'équipe) ainsi que le contenu de l'email.

### 3. Intégration Collaborative dans Microsoft Teams

Cette étape permet de diffuser la veille à l'ensemble de l'équipe IT.

* **a. Création de l'Équipe et du Canal**
    Dans Microsoft Teams, créez une nouvelle équipe `privée` (ex: `PINEAL`) avec un `canal dédié` à la veille (ex: `VEILLE-EQUIPE IT`).
    Choisir, `Seuls les propriétaires peuvent publier des messages`.

* **b. Configuration du Connecteur RSS**
    1.  Dans les paramètres du canal, allez dans "Connecteurs" et ajoutez le connecteur **RSS**.
    2.  Configurez le connecteur :
        * **Nom :** `CyberVeille`
        * **Adresse du flux RSS :** `https://www.cert.ssi.gouv.fr/cti/feed/`
        * **Fréquence :** `Every hour` (Toutes les heures).

## 🧪 Résultat Final

Le système est maintenant opérationnel :
* Le canal Teams **VEILLE-EQUIPE IT** reçoit automatiquement et affiche les nouvelles publications du CERT-FR toutes les heures.
* Dès qu'une nouvelle publication apparaît sur le flux, l'applet IFTTT se déclenche et envoie une notification par email.
* Le tableau de bord Feedly offre une vue d'ensemble de toutes les sources pour une consultation plus large.

✅ **Un système de veille cyber automatisé et multi-canal est en place pour une réactivité optimale face aux nouvelles menaces.**
