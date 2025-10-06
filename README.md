# LAB : Supervision d'Infrastructure avec Zabbix

Ce laboratoire décrit la mise en place d'une solution de supervision centralisée avec Zabbix. L'objectif est de surveiller en temps réel l'état de santé et les performances des serveurs critiques de l'infrastructure, notamment le contrôleur de domaine Active Directory et le serveur ITSM GLPI.

Les étapes clés incluent :

  * La préparation d'une machine virtuelle dédiée sur `Debian 13` pour héberger le serveur Zabbix.
  * L'installation des composants Zabbix (serveur, frontend, base de données).
  * Le déploiement des **agents Zabbix** sur les serveurs cibles : un serveur `Windows Server 2022` (Active Directory) et un serveur `Debian 13` (GLPI).
  * La configuration des hôtes dans l'interface web de Zabbix.
  * L'association des modèles de supervision (templates) pour commencer la collecte de métriques pertinentes (CPU, RAM, disque, etc.).

## 🎯 Objectif

L'objectif est de déployer un serveur de supervision Zabbix fonctionnel, capable de centraliser les alertes et les indicateurs de performance des serveurs clés. Cette mise en place doit permettre un suivi proactif de la disponibilité et de la consommation des ressources de l'Active Directory et de GLPI.

<img width="1280" height="644" alt="image" src="https://github.com/user-attachments/assets/df804544-3884-4206-a942-c6ca253f96b8" />


## 🛠️ Prérequis

  * Une machine virtuelle dédiée pour le serveur Zabbix (Debian 13).
  * Le serveur Active Directory (`par-dc-win01`) du LAB précédent doit être fonctionnel.
  * Le serveur GLPI (`par-glpi-01`) du LAB précédent doit être fonctionnel.
  * Une connectivité réseau entre les trois machines.

## ⚙️ Étapes d'installation et de configuration

### 1\. Déploiement du Serveur Zabbix

Une nouvelle VM Debian 13, nommée `par-zbx-01`, est installée. L'installation de Zabbix Server, Frontend et des dépendances (Apache, MariaDB, PHP) est réalisée en suivant la documentation officielle. L'étape finale de configuration se fait via l'interface web.

### 2\. Installation de l'Agent Zabbix sur Windows Server (`par-dc-01`)

L'agent Zabbix est installé sur le contrôleur de domaine pour permettre la collecte de données.

  * **a. Téléchargement et installation**
    L'agent MSI pour Windows est téléchargé depuis le [site officiel de Zabbix](https://www.zabbix.com/download_agents) et installé. Durant l'installation, il faut renseigner :

      * **Zabbix server IP:** `192.168.1.59`
      * **Hostname:** `par-dc-win01`

  * **b. Configuration du Pare-feu**
    Il est nécessaire de vérifier que le pare-feu Windows autorise les connexions entrantes sur le port **TCP/10050** pour que le serveur Zabbix puisse communiquer avec l'agent.

### 3\. Installation de l'Agent Zabbix sur Debian (`par-glpi-01`)

  * **a. Installation du paquet**

    ```bash
    sudo apt install zabbix-agent -y
    ```

  * **b. Configuration de l'agent**
    Le fichier de configuration de l'agent doit être modifié pour pointer vers le serveur Zabbix.

    ```bash
    sudo nano /etc/zabbix/zabbix_agentd.conf
    ```

    Modifiez les lignes suivantes :

    ```ini
    Server=192.168.1.59
    Hostname=par-glpi-01
    ```

  * **c. Redémarrage du service**

    ```bash
    sudo systemctl restart zabbix-agent
    ```

## 🧪 Configuration et Vérification dans l'Interface Zabbix

Dans l'interface web de Zabbix, les deux serveurs sont ajoutés en tant qu'hôtes pour démarrer la supervision.

  * **a. Ajout de l'hôte Windows (`par-dc-01`)**

      * **Hostname:** `par-dc-01`
      * **Groups:** `Windows servers`
      * **Interfaces:** `Agent` avec l'adresse IP `192.168.1.50`
      * **Templates:** `Windows by Zabbix agent`

  * **b. Ajout de l'hôte Linux (`par-glpi-01`)**

      * **Hostname:** `par-glpi-01`
      * **Groups:** `Linux servers`
      * **Interfaces:** `Agent` avec l'adresse IP `192.168.1.22`
      * **Templates:** `Linux by Zabbix agent`

Une fois les hôtes ajoutés, l'icône "Availability" doit passer au vert, indiquant que la communication est établie et que la collecte de données a commencé.

✅ **Les serveurs critiques sont maintenant supervisés par Zabbix, permettant un suivi proactif de leur état.**
