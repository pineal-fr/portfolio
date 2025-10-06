# LAB : Installation et Configuration de GLPI sur Debian 13

Ce laboratoire détaille les étapes pour installer et configurer un serveur de gestion de parc informatique (ITSM) GLPI sur une machine virtuelle Debian 13.
Il couvre non seulement le déploiement de la solution, mais aussi les bonnes pratiques de sécurité fondamentales, telles que la création d'utilisateurs dédiés et la sécurisation des accès.
L'objectif final est d'obtenir une instance GLPI fonctionnelle, avec son propre agent d'inventaire installé et configuré sur le serveur hôte lui-même.

Les étapes clés incluent :

* La préparation d'un environnement minimaliste sur ` Debian 13 "Trixie" ` sans interface graphique pour réduire la surface d'attaque.

* La création d'un utilisateur d'administration sécurisé `(pinealadmin)` avec des privilèges sudo, destiné aux opérations de maintenance via une connexion SSH.

* L'installation d'une pile LAMP (Apache, MariaDB, PHP) et sa configuration pour héberger GLPI.

* La sécurisation de la base de données par la création d'un utilisateur dédié `(glpiuser)` avec des droits restreints, évitant ainsi l'utilisation du compte root.

* L'application des mesures de sécurité post-installation, comme la `suppression du script d'installation` et la `modification des mots de passe par défaut`.

<img width="1852" height="962" alt="image" src="https://github.com/user-attachments/assets/387f62bb-8c28-4186-a657-7f9bd2cd268a" />


## 🎯 Objectif

L'objectif est de déployer un serveur GLPI fonctionnel pour la traçabilité et l'inventaire du parc informatique. Le serveur doit être installé sur une Debian 13 "Trixie" sans interface graphique et doit lui-même être inventorié dans l'outil.

## 🛠️ Prérequis

* Hyperviseur de type 1 (Proxmox, ESXi) ou de type 2 (VMware Workstation, VirtualBox).
* Image ISO de Debian 13 (Netinstall) : [https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-13.1.0-amd64-netinst.iso](https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-13.1.0-amd64-netinst.iso)

## ⚙️ Étapes d'installation

### 1. Création de la Machine Virtuelle

Une VM a été créée avec la configuration suivante pour héberger GLPI:
* **Nom de la VM :** `par-glpi-01` (respectant la convention RFC1178)
* **Système d'exploitation :** Debian 13 "Trixie"
* **Mémoire :** 4 Go de RAM
* **Processeurs :** 2 vCPU
* **Disque dur :** 25 Go
* **Réseau :** Accès Bridged

### 2. Installation de Debian 13

L'installation de Debian a été réalisée sans interface graphique. Seuls les paquets suivants ont été sélectionnés:
* Serveur SSH
* Utilitaires usuels du système

Un utilisateur non-root `pinealadmin` a été créé pour les opérations courantes.

### 3. Installation de la pile LAMP

Une fois le système installé, une connexion SSH est établie pour installer la pile LAMP (Linux, Apache, MariaDB, PHP).

* **a. Connexion SSH**

Récupération de l'adresse IP du serveur :
`ip a`

### Connexion depuis une machine cliente (ici, via PowerShell) :
`ssh pinealadmin@10.10.20.18`

* **b. Installation et configuration d'Apache**

Mise à jour des paquets et installation d'Apache2 :
`sudo apt update && sudo apt upgrade -y`
`sudo apt install apache2 -y`

Vérification du statut du service Apache :
`sudo systemctl status apache2`

La page par défaut d'Apache doit être accessible via l'IP du serveur.

* **c. Installation et sécurisation de MariaDB**
`sudo apt install mariadb-server -y`
`sudo mariadb-secure-installation`

**Lors du script de sécurisation, les options suivantes ont été choisies :**
`Changer le mot de passe root ? 
Oui`
`Supprimer les utilisateurs anonymes ? 
Oui`
`Désactiver la connexion root à distance ? 
Oui` 
`Supprimer la base de données de test ? 
Oui` 
`Recharger les privilèges ? 
Oui` 

* **d. Installation de PHP et des extensions nécessaires**
`sudo apt install php libapache2-mod-php php-mysql php-curl php-gd php-xml php-intl php-mbstring -y`

**Vérification de la version de PHP :**
`php -v`

**Création d'une page de test**
`info.php` pour valider l'installation, puis suppression pour des raisons de sécurité.
`sudo nano /var/www/html/info.php`

Contenu du fichier :
` <?php
phpinfo();
?> `

**Suppression du fichier après vérification :**
`sudo rm /var/www/html/info.php`

### 4. Installation de GLPI 
* **a. Téléchargement et extraction**
`cd /tmp`
`wget [https://github.com/glpi-project/glpi/releases/download/10.0.19/glpi-10.0.19.tgz](https://github.com/glpi-project/glpi/releases/download/10.0.19/glpi-10.0.19.tgz)`
`sudo tar -xvf glpi-10.0.19.tgz -C /var/www/html/`

* **b. Attribution des permissions**
`sudo chown -R www-data:www-data /var/www/html/glpi`
`sudo chmod -R 755 /var/www/html/glpi`

* **c. Création de la base de données**

**Connexion à MariaDB et création de la base de données et de l'utilisateur dédié.**
`sudo mariadb`
`CREATE DATABASE glpidb;`
`CREATE USER 'glpiuser'@'localhost' IDENTIFIED BY 'VOTRE_MOT_DE_PASSE_SECURISE';`
`GRANT ALL PRIVILEGES ON glpidb.* TO 'glpiuser'@'localhost'; `
`FLUSH PRIVILEGES; `
` EXIT;`

**5. Finalisation via l'interface Web**
*Accédez à* ` http://<IP_SERVEUR>/glpi et suivez les étapes de l'installateur web.`

*Après l'installation, des actions de sécurité post-installation sont nécessaires :*

*Supprimer le fichier d'installation:*

`sudo rm /var/www/html/glpi/install/install.php`
Changer les mots de passe des comptes par défaut (glpi, tech, normal, post-only).

***6. Installation et configuration de GLPI Inventory***
* **a. Installation du plugin**

Depuis l'interface GLPI, accédez à la Marketplace, recherchez et installez le plugin 
GLPI Inventory. Si le module PHP `bz2` est manquant, installez-le :
`sudo apt install php8.4-bz2 -y`
`sudo systemctl restart apache2`

Activez ensuite le plugin.
* **b. Installation de l'agent sur le serveur GLPI**

*Installation de Perl, puis téléchargement et exécution du script d'installation de l'agent.*
`cd /opt`;
`sudo wget [https://github.com/glpi-project/glpi-agent/releases/download/1.15/glpi-agent-1.15-linux-installer.pl](https://github.com/glpi-project/glpi-agent/releases/download/1.15/glpi-agent-1.15-linux-installer.pl)`;
`sudo apt install perl -y`;
`sudo perl glpi-agent-1.15-linux-installer.pl`

**Durant l'installation, renseignez l'URL du serveur :**
`http://192.168.1.22/glpi`

* **c. Lancement et vérification**

*Lancez un inventaire forcé pour tester :*
`sudo glpi-agent --force`

*Vérifiez les logs :*
`tail -n 20 /var/log/glpi-agent/glpi-agent.log`

✅**Le serveur `par-glpi-01` doit maintenant apparaître dans l'inventaire GLPI.**
