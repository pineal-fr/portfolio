# LAB-1/03 : Déploiement d'Active Directory et DHCP sur Windows Server

Ce laboratoire détaille la mise en place des services centraux d'une infrastructure Microsoft : l'annuaire **Active Directory Domain Services (AD DS)** pour la gestion des identités et le service **DHCP** pour l'attribution automatique des adresses IP. L'ensemble est déployé sur un serveur **Windows Server 2022**.

Les étapes clés incluent :

* L'installation des rôles **AD DS** et **DHCP** sur le serveur.
* La promotion du serveur en tant que **Contrôleur de Domaine** pour une nouvelle forêt `pineal.lan`.
* La création d'une structure logique avec des **Unités d'Organisation (OU)** pour chaque service de l'entreprise.
* La configuration des **étendues DHCP** pour distribuer les adresses IP correspondant à chaque VLAN du réseau.

## 🎯 Objectif
L'objectif est de centraliser la gestion des utilisateurs et des ordinateurs au sein du domaine `pineal.lan` et d'automatiser la configuration réseau des postes clients. Cela permet de simplifier l'administration, de renforcer la sécurité et de garantir une attribution cohérente des adresses IP sur les différents segments du réseau.

<img width="1280" height="720" alt="image" src="https://github.com/user-attachments/assets/20f72b09-f08d-49c4-94b4-cc50e567075c" />


## 🛠️ Prérequis
* Une machine virtuelle avec **Windows Server 2022** installé.
* Des droits d'administration sur ce serveur.
* Une configuration réseau permettant au serveur d'avoir une adresse IP statique.

## ⚙️ Étapes d'installation et de configuration

### 1. Préparation du Serveur
Une VM nommée `par-dc-win01` est préparée avec une installation de Windows Server 2022 et une adresse IP statique lui est assignée.

### 2. Installation des Rôles AD DS et DHCP
Via le "Gestionnaire de serveur" (Server Manager), les deux rôles suivants sont installés :
* **Services de domaine Active Directory (AD DS)**
* **Serveur DHCP**

### 3. Promotion en Contrôleur de Domaine
Une fois les rôles installés, le serveur est promu en contrôleur de domaine. Cette étape consiste à créer une **nouvelle forêt** nommée **`pineal.lan`**.

### 4. Structuration de l'Active Directory
Pour organiser les objets de l'annuaire (utilisateurs, groupes, ordinateurs), des **Unités d'Organisation (OU)** sont créées pour chaque service de l'entreprise :
* `DI` (Développement et Ingénierie)
* `VM` (Vente et Marketing)
* `SC` (Support Client)
* `RH` (Ressources Humaines)
* `FC` (Finance et Comptabilité)

### 5. Configuration du Serveur DHCP
Le service DHCP est configuré pour servir les adresses IP à chaque VLAN. Cinq étendues (scopes) sont créées avec les paramètres suivants :

| Nom de l'étendue | VLAN (Service) | Plage d'Adresses IP | Passerelle par Défaut | Serveur DNS |
| :--- | :--- | :--- | :--- | :--- |
| VLAN10-DI | 10 (DI) | `192.168.10.100` - `192.168.10.200` | `192.168.10.1` | `192.168.1.10` |
| VLAN20-VM | 20 (VM) | `192.168.20.100` - `192.168.20.200` | `192.168.20.1` | `192.168.1.10` |
| VLAN30-SC | 30 (SC) | `192.168.30.100` - `192.168.30.200` | `192.168.30.1` | `192.168.1.10` |
| VLAN40-RH | 40 (RH) | `192.168.40.100` - `192.168.40.200` | `192.168.40.1` | `192.168.1.10` |
| VLAN50-FC | 50 (FC) | `192.168.50.100` - `192.168.50.200` | `192.168.50.1` | `192.168.1.10` |

✅ **Le domaine `pineal.lan` est opérationnel et le service DHCP est configuré pour automatiser l'adressage IP sur le réseau.**
