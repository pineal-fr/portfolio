# LAB-1/05 : Haute Disponibilité du Routage avec HSRP

Ce laboratoire est consacré à l'élimination du routeur comme point de défaillance unique (Single Point of Failure) en implémentant une solution de redondance de passerelle. Nous utiliserons pour cela le protocole **HSRP (Hot Standby Router Protocol)** sur deux routeurs Cisco.

Les étapes clés incluent :

  * La conception d'une topologie réseau simple dans **Packet Tracer** pour simuler l'environnement.
  * La configuration d'un **routeur actif** (maître) avec une priorité élevée et l'option de préemption.
  * La configuration d'un **routeur de secours** (standby) qui prendra le relais en cas de panne.
  * L'utilisation d'une **passerelle virtuelle** commune pour les clients du réseau.
  * La validation du bon fonctionnement et la réalisation d'un **test de basculement** (failover).

## 🎯 Objectif

L'objectif est de déployer une solution de redondance HSRP pour garantir la continuité du service et l'accès à Internet pour les utilisateurs, même en cas de défaillance du routeur principal.

<img width="960" height="454" alt="image" src="https://github.com/user-attachments/assets/80fb7d90-0765-49ba-879c-4734abc3dacc" />


## 🛠️ Prérequis

  * Un outil de simulation réseau comme **Cisco Packet Tracer**.
  * Les composants nécessaires : 2 routeurs, 1 commutateur, 1 PC client.

## 🏗️ Architecture et Planification

### 1\. Topologie

L'environnement de test est composé des éléments suivants dans Packet Tracer :

  * **2 Routeurs (modèle 2911) :** `Router1` (Actif) et `Router2` (Standby).
  * **1 Commutateur (modèle 2960)**.
  * **1 PC Client :** `PC-Admin`.

### 2\. Planification du Projet

L'implémentation de cette solution a suivi un plan de projet structuré en quatre phases principales : Planification, Implémentation, Validation et Documentation.
Le projet a été planifié pour démarrer dans 25 jours.

## ⚙️ Configuration Technique HSRP

### 1\. Configuration du PC Client

Le PC est configuré pour utiliser l'**adresse IP virtuelle** comme passerelle par défaut.

  * **Passerelle par défaut :** `192.168.1.1`

### 2\. Configuration du Routeur 1 (Actif)

Ce routeur est configuré avec une priorité plus élevée (110) pour être élu routeur actif et l'option `preempt` pour reprendre son rôle dès qu'il est de nouveau disponible.

```cisco
enable
configure terminal
interface GigabitEthernet0/0
 ip address 192.168.1.2 255.255.255.0
 no shutdown
 standby 1 ip 192.168.1.1
 standby 1 priority 110
 standby 1 preempt
exit
```

### 3\. Configuration du Routeur 2 (Standby)

Ce routeur est configuré avec la priorité par défaut (100) pour rester en mode "standby" tant que le routeur 1 est opérationnel.

```cisco
enable
configure terminal
interface GigabitEthernet0/0
 ip address 192.168.1.3 255.255.255.0
 no shutdown
 standby 1 ip 192.168.1.1
exit
```

## 🧪 Vérifications et Test de Basculement

  * **a. Vérification de l'état HSRP**
    La commande `show standby` est exécutée sur les deux routeurs pour confirmer leurs rôles respectifs : `State is Active` pour Router1 et `State is Standby` for Router2

      * **b. Test de Basculement (Failover)**

    <!-- end list -->

    1.  Un `ping` en continu est lancé depuis le PC client vers l'adresse IP virtuelle (`ping -t 192.168.1.1`).
    2.  La connexion physique entre le **Router1** et le commutateur est supprimée pour simuler une panne.
    3.  On observe une interruption minimale du ping (une ou deux pertes de paquets) avant que la connectivité ne soit rétablie.
    4.  Une nouvelle vérification avec `show standby` sur le **Router2** confirme qu'il est bien passé à l'état `Active`.

    ✅ **La redondance de la passerelle est fonctionnelle, garantissant une haute disponibilité de l'accès réseau.**
