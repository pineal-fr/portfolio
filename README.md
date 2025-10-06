# LAB : Sauvegarde de VMs avec Veeam Backup & Replication

Ce laboratoire détaille la mise en place d'une solution de sauvegarde et de restauration pour un environnement virtualisé. En utilisant **Veeam Backup & Replication Community Edition**, nous allons configurer la protection des deux machines virtuelles critiques de notre infrastructure : le contrôleur de domaine Windows (`par-dc-win01`) et le serveur GLPI sous Debian (`par-glpi-01`).

Les étapes clés incluent :

* L'installation de la console **Veeam B&R** sur le serveur Windows.
* La configuration d'un **dépôt de sauvegarde local** (Repository) pour stocker les backups.
* La création d'un **groupe de protection** pour gérer et déployer automatiquement les agents Veeam.
* Le déploiement des **agents de sauvegarde** sur les machines Windows et Debian.
* La création et la planification d'un **job de sauvegarde** pour automatiser le processus.
* La réalisation d'un **test de restauration** au niveau fichier pour valider l'intégrité des sauvegardes.

## 🎯 Objectif

L'objectif est d'établir une stratégie de sauvegarde fiable et automatisée pour les serveurs critiques. Cela garantit la capacité de restaurer des données ou des systèmes complets en cas d'incident (panne matérielle, cyberattaque, erreur humaine), assurant ainsi la continuité de l'activité.

<img width="750" height="422" alt="image" src="https://github.com/user-attachments/assets/c072cd1b-c35c-4b70-97fd-c4f0cb404183" />


## 🛠️ Prérequis

* Le serveur Active Directory (`par-dc-win01`) doit être fonctionnel. C'est sur cette machine que Veeam B&R sera installé.
* Le serveur GLPI (`par-glpi-01`) doit être fonctionnel.
* L'installeur ISO de **Veeam Backup & Replication Community Edition**.
* Des informations d'identification avec des droits administratifs sur les deux serveurs cibles.

## ⚙️ Étapes d'installation et de configuration

### 1. Préparation et Installation de Veeam

Après avoir téléchargé l'ISO, l'installation de Veeam B&R est lancée sur le serveur `par-dc-win01`. Un dossier local est également créé à la racine du disque pour accueillir les sauvegardes.
* **Dossier de sauvegarde :** `C:\_VeeamBackups`

### 2. Configuration du Dépôt de Sauvegarde (Repository)

Il s'agit de déclarer à Veeam où stocker les fichiers de sauvegarde.

* **a. Lancement de l'assistant**
    Dans la console Veeam, naviguez vers **Backup Infrastructure**, faites un clic droit sur **Backup Repositories** et sélectionnez `Add backup repository...`.

* **b. Configuration**
    * **Type :** `Direct attached storage` > `Microsoft Windows`.
    * **Nom :** `Backup local`.
    * **Chemin :** Parcourir et sélectionner le dossier `C:\_VeeamBackups`.
    * Suivre l'assistant jusqu'à la fin pour valider la création.

### 3. Création du Groupe de Protection

Ce groupe permet de gérer le déploiement des agents Veeam sur les machines à protéger.

* **a. Lancement de l'assistant**
    Naviguez vers **Inventory**, faites un clic droit sur **Physical Infrastructure** et sélectionnez `Create protection group`.

* **b. Configuration**
    * **Nom :** `Serveurs Critiques PINEAL`.
    * **Type :** `Servers`.
    * **Computers :** Ajoutez les deux serveurs via leurs adresses IP (`192.168.1.10` pour Windows et `192.168.1.22` pour Debian) en fournissant les informations d'identification administratives requises (compte de domaine pour Windows, compte `pinealadmin` pour Debian/SSH).
    * Veeam se connectera alors à chaque machine pour y déployer l'agent de sauvegarde.

### 4. Création du Job de Sauvegarde

Le "job" est la tâche qui exécute la sauvegarde selon les paramètres définis.

* **a. Lancement de l'assistant**
    Naviguez vers **Home**, cliquez sur **Backup Job** > `Windows computer...`.

* **b. Configuration**
    * **Nom :** `Sauvegarde Journalière des Serveurs`.
    * **Computers :** Ajoutez le groupe de protection `Serveurs Critiques PINEAL` créé précédemment.
    * **Mode :** `Server`.
    * **Storage :** Sélectionnez le dépôt `Backup local` comme destination.
    * **Schedule :** Configurez une exécution automatique quotidienne (par exemple, tous les jours à 22h00).
    * Cochez `Run the job when I click Finish` pour lancer la première sauvegarde immédiatement.

## 🧪 Test de Restauration de Fichier

Une sauvegarde n'est fiable que si sa restauration est testée et fonctionnelle.

* **a. Simulation de perte de données**
    1. Créez un fichier `test.txt` sur le bureau du serveur Windows (`par-dc-win01`).
    2. Lancez manuellement le job de sauvegarde pour inclure ce nouveau fichier.
    3. Une fois la sauvegarde réussie, supprimez le fichier `test.txt`.

* **b. Processus de restauration**
    1. Dans la console Veeam, allez dans `Home > Backups > Disk`.
    2. Faites un clic droit sur la sauvegarde du serveur `par-dc-win01` et choisissez `Restore guest files > Windows`.
    3. L'explorateur de sauvegarde s'ouvre. Naviguez jusqu'au bureau (`C:\Users\Administrator\Desktop`).
    4. Retrouvez `test.txt`, faites un clic droit dessus et sélectionnez `Restore > Overwrite`.

Le fichier doit réapparaître sur le bureau, confirmant le bon fonctionnement du processus.

✅ **Une stratégie de sauvegarde et de restauration fonctionnelle est en place pour les VMs critiques.**
