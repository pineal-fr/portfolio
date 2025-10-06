# Projet : Déploiement et Sécurisation d'une Infrastructure (LAB-1)

Bienvenue dans ce projet qui regroupe une série de laboratoires pratiques (Travaux Pratiques) dédiés à l'administration d'infrastructures sécurisées. Chaque laboratoire est conçu pour démontrer des compétences spécifiques allant de la mise en place de services fondamentaux à la gestion de la cybersécurité.

L'ensemble du projet est organisé de manière chronologique, suivant les étapes de construction et de sécurisation d'une infrastructure d'entreprise simulée.

## 🏗️ Structure du Projet

Ce dépôt est structuré de manière hiérarchique. La branche `main` sert de point d'entrée, et cette branche `LAB-1` sert de base pour tous les laboratoires. Chaque laboratoire est isolé dans sa propre branche, nommée selon la convention `LAB-1/XX-Nom-du-Lab`, pour une clarté et une organisation optimales.

Pour explorer un laboratoire, il suffit de changer de branche en utilisant le sélecteur en haut à gauche de la page, ou de cliquer sur les liens directs dans la liste ci-dessous.

## 🔬 Arborescence des Laboratoires

Voici la liste des laboratoires sous-jacents, présentés dans leur ordre chronologique.

---

### 1. 📦 Installation d'un serveur ITSM sur Debian
* **Branche :** [`LAB-1/01-Installation-GLPI`](../blob/LAB-1/01-Installation-GLPI/README.md)
* **Description :** Ce lab couvre le déploiement complet d'un serveur de gestion de parc informatique **GLPI** sur une machine virtuelle **Debian 13**. Il inclut l'installation du système sans interface graphique, la configuration d'une pile **LAMP**, la sécurisation de la base de données et l'installation de l'agent d'inventaire.

---

### 2. 🌐 Segmentation et Redondance Réseau avec Cisco
* **Branche :** [`LAB-1/02-Segmentation-VLAN-LACP`](../blob/LAB-1/02-Segmentation-VLAN-LACP/README.md)
* **Description :** Mise en place d'une architecture réseau segmentée et résiliente sur des commutateurs **Cisco**. Ce lab détaille la création de **VLANs** par service, la synchronisation via **VTP**, la mise en place d'une agrégation de liens **LACP** et la sécurisation de l'administration via **SSH**.

---

### 3. 🔐 Gestion Centralisée avec Active Directory et DHCP
* **Branche :** [`LAB-1/03-Active-Directory-DHCP`](../blob/LAB-1/03-Active-Directory-DHCP/README.md)
* **Description :** Déploiement des services d'annuaire **Active Directory (AD DS)** et de distribution d'adresses **DHCP** sur **Windows Server**. Le lab inclut la création d'une forêt, la structuration en Unités d'Organisation (OU) et la configuration des étendues DHCP pour chaque VLAN.

---

### 4. 💾 Stratégie de Sauvegarde avec Veeam
* **Branche :** [`LAB-1/04-Sauvegarde-Veeam`](../blob/LAB-1/04-Sauvegarde-Veeam/README.md)
* **Description :** Implémentation d'une solution de sauvegarde et de restauration avec **Veeam Backup & Replication**. Ce lab couvre la configuration d'un dépôt de sauvegarde, la création de jobs, le déploiement d'agents sur Windows et Linux, et la validation par un test de restauration de fichier.

---

### 5. 🔄 Haute Disponibilité du Routage avec HSRP
* **Branche :** [`LAB-1/05-Redondance-HSRP`](../blob/LAB-1/05-Redondance-HSRP/README.md)
* **Description :** Élimination du point de défaillance unique (SPOF) au niveau de la passerelle réseau grâce au protocole **HSRP (Hot Standby Router Protocol)**. Ce lab montre comment configurer deux routeurs pour assurer une redondance active/passive et tester le basculement automatique (failover).

---

### 6. 📊 Supervision d'Infrastructure avec Zabbix
* **Branche :** [`LAB-1/06-Supervision-Zabbix`](../blob/LAB-1/06-Supervision-Zabbix/README.md)
* **Description :** Déploiement d'une solution de monitoring centralisée avec **Zabbix** pour superviser les serveurs critiques (Active Directory et GLPI). Le lab couvre l'installation du serveur Zabbix et le déploiement des agents sur les hôtes Windows et Linux.

---

### 7. 🛡️ Audit de Sécurité Web avec OWASP ZAP
* **Branche :** [`LAB-1/07-Audit-ZAP`](../blob/LAB-1/07-Audit-ZAP/README.md)
* **Description :** Réalisation d'un audit de vulnérabilités sur l'application web GLPI à l'aide d'**OWASP ZAP** depuis une machine **Kali Linux**. Le lab se conclut par une analyse des failles découvertes et la proposition d'un plan de remédiation.

---

### 8. 📡 Automatisation de la Veille en Cybersécurité
* **Branche :** [`LAB-1/08-Veille-Cyber-RSS`](../blob/LAB-1/08-Veille-Cyber-RSS/README.md)
* **Description :** Création d'un système de veille automatisé pour rester informé des dernières menaces. Ce lab combine l'agrégation de flux **RSS** avec **Feedly**, l'automatisation d'alertes par email avec **IFTTT** et l'intégration dans un canal **Microsoft Teams**.

---
