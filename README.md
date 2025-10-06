# LAB : Audit de Vulnérabilités Web avec OWASP ZAP

Ce laboratoire décrit la procédure pour réaliser un audit de sécurité sur une application web, en l'occurrence le serveur GLPI précédemment installé. Nous utiliserons pour cela l'outil OWASP ZAP (ZAPROXY) depuis une machine virtuelle Kali Linux afin d'identifier, d'évaluer et de proposer des solutions aux vulnérabilités découvertes.

Les étapes clés incluent :

  * La préparation d'un environnement d'audit sécurisé sur `Kali Linux`.
  * La création d'un utilisateur dédié `(pinealadmin)` pour les opérations.
  * L'installation et la configuration de l'outil d'analyse de vulnérabilités **OWASP ZAP**.
  * L'exécution de scans passifs et actifs (Spider, AJAX Spider, Active Scan) contre le serveur GLPI.
  * L'analyse des alertes de sécurité générées par l'outil.
  * La création d'un rapport synthétisant les failles et la formulation d'un **plan de remédiation**.

<img width="860" height="277" alt="image" src="https://github.com/user-attachments/assets/18096f92-559e-4b72-a705-afd7be9ad4b7" />


## 🎯 Objectif

L'objectif de ce laboratoire est de mener un audit de sécurité de type "boîte grise" sur l'application web GLPI. Il s'agit de détecter les vulnérabilités potentielles, d'évaluer leur niveau de criticité, et de rédiger des préconisations techniques claires pour renforcer la sécurité du serveur web.

## 🛠️ Prérequis

  * Hyperviseur (VMware Workstation, VirtualBox, etc.).
  * Machine virtuelle Kali Linux (image disponible sur [le site officiel de Kali](https://www.kali.org/get-kali/#kali-virtual-machines)).
  * Le serveur GLPI du LAB 1 doit être fonctionnel et accessible sur le réseau.

## ⚙️ Démarche de l'Audit

### 1\. Préparation de l'environnement Kali Linux

Une machine virtuelle Kali Linux est déployée. Les premières étapes consistent à sécuriser l'environnement de travail.

  * **a. Création d'un utilisateur non-root**
    ```bash
    sudo adduser pinealadmin
    ```
  * **b. Ajout de l'utilisateur au groupe sudo**
    ```bash
    sudo usermod -aG sudo pinealadmin
    ```
  * **c. Mise à jour du système**
    ```bash
    sudo apt update && sudo apt upgrade -y
    ```

### 2\. Installation et Lancement de ZAPROXY

OWASP ZAP est installé via le gestionnaire de paquets `apt`.

  * **a. Installation de ZAPROXY**
    ```bash
    sudo apt install zaproxy
    ```
  * **b. Lancement de l'application**
    ```bash
    owasp-zap
    ```
    Au premier lancement, il est demandé de configurer la persistance de la session.

### 3\. Exploration et Scan du site GLPI

L'audit se déroule en plusieurs phases pour découvrir et attaquer l'application.

  * **a. Exploration Manuelle et Spidering**
    L'exploration manuelle est lancée en ciblant l'URL du serveur GLPI (`http://192.168.1.22/glpi`). Ensuite, des scans **Spider** et **AJAX Spider** sont exécutés pour découvrir l'arborescence complète du site et les points d'entrée potentiels.

  * **b. Scan Actif (Attaque)**
    Un scan actif est lancé sur le site découvert. ZAP envoie une série de charges utiles pour tester activement les vulnérabilités connues (injections SQL, XSS, etc.). Cette étape peut être longue.

### 4\. Analyse des Résultats et Plan de Remédiation

Les vulnérabilités trouvées sont listées dans l'onglet **"Alertes"**.
Un rapport est ensuite généré pour analyse, nous pouvons le placer au format HTML en cliquant sur l'onglet `Report` puis `Generate Report`.

**Vulnérabilités Moyennes identifiées et plan de remédiation :**

| Vulnérabilité (Alerte ZAP) | Criticité | Risque Associé | Préconisation Technique / Solution |
| :--- | :--- | :--- | :--- |
| **Content Security Policy (CSP) Header Not Set** | Moyenne | Augmente le risque d'attaques par injection de script (XSS) car le navigateur n'a aucune instruction sur les sources de contenu légitimes. | Ajouter un en-tête HTTP `Content-Security-Policy` strict dans la configuration Apache, par exemple : `Header set Content-Security-Policy "default-src 'self';"` |
| **Missing Anti-clickjacking Header** | Moyenne | Permet des attaques de type "Clickjacking", où un attaquant intègre GLPI dans une `<iframe>` sur une page malveillante pour tromper l'utilisateur. | Ajouter l'en-tête `X-Frame-Options` dans la configuration Apache pour interdire l'affichage du site dans une frame externe : `Header always set X-Frame-Options "SAMEORIGIN"` |
| **Application Error Disclosure** | Moyenne | Un message d'erreur peut divulguer des informations techniques sensibles (ex: chemin d'un fichier sur le serveur), qui peuvent être utilisées pour affiner d'autres attaques. | Mettre en place des pages d'erreur personnalisées et génériques dans la configuration Apache qui ne divulguent aucune information interne. |

✅ **L'audit de sécurité a été réalisé et un plan de remédiation a été établi pour corriger les vulnérabilités identifiées.**
