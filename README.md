# LAB-1/02 : Segmentation Réseau avec VLAN, VTP et LACP sur Cisco

Ce laboratoire détaille les étapes pour configurer deux commutateurs Cisco afin de segmenter un réseau d'entreprise.
Il couvre la création de réseaux locaux virtuels (VLANs), leur synchronisation automatique via le protocole VTP (VLAN Trunking Protocol), la mise en place d'une agrégation de liens redondante avec LACP (Link Aggregation Control Protocol) et la sécurisation de l'administration des équipements.

Les étapes clés incluent :

  * La définition d'une topologie réseau et d'un plan d'adressage optimisé (VLSM).
  * La configuration d'un commutateur en tant que **serveur VTP** (`sirius`) pour centraliser la gestion des VLANs.
  * La configuration d'un second commutateur en tant que **client VTP** (`vega`) pour recevoir automatiquement la configuration des VLANs.
  * La mise en place d'un **Port-Channel (LACP)** pour assurer une liaison redondante et performante entre les deux commutateurs.
  * La sécurisation de l'accès administratif via la création de comptes locaux et l'activation du protocole **SSH**.
  * Les tests de validation pour vérifier le bon fonctionnement de l'ensemble.


<img width="1342" height="634" alt="image" src="https://github.com/user-attachments/assets/e28b55fd-7400-4a00-8c7e-75acede163c8" />


## 🎯 Objectif

L'objectif est de déployer une infrastructure réseau segmentée et résiliente. Chaque service de l'entreprise doit être isolé dans son propre VLAN, les commutateurs doivent synchroniser leur base de données VLAN, et la liaison entre eux doit être redondante pour éviter tout point de défaillance unique (Single Point of Failure).

## 🛠️ Prérequis

  * Un outil de simulation réseau comme **Cisco Packet Tracer** ou un environnement physique avec des équipements Cisco (ex: commutateurs 2960).

## 🏗️ Architecture et Plan d'Adressage

### 1\. Topologie

  * **2 Commutateurs Cisco 2960 :** `sirius` (Serveur VTP) et `vega` (Client VTP).
  * **6 PCs :** 5 pour les services (`di-pc-001`, `vm-pc-001`, etc.) et 1 pour l'administration (`admin-pc-001`).
  * **Agrégation de liens (LACP) :** Entre les ports `GigabitEthernet0/1-2` de `sirius` et `vega`.

### 2\. Plan d'Adressage VLSM (Variable Length Subnet Mask)

| Service | VLAN ID | Réseau IP / Masque | Plage d'hôtes |
| :--- | :--- | :--- | :--- |
| DI | 10 | `192.168.10.0 /25` | `192.168.10.1` - `126` |
| VM | 20 | `192.168.10.128 /26` | `192.168.10.129` - `190` |
| SC | 30 | `192.168.10.192 /27` | `192.168.10.193` - `222` |
| RH | 40 | `192.168.10.224 /27` | `192.168.10.225` - `254` |
| FC | 50 | `192.168.11.0 /27` | `192.168.11.1` - `30` |
| Admin | 1 | `192.168.1.0 /24` | - |

## ⚙️ Configuration des Commutateurs

### 1\. Configuration de `sirius` (Serveur VTP)

#### a. Identité et Sécurité

```cisco
enable
configure terminal
hostname sirius
enable secret VTS78SecretCODE
username admin privilege 15 secret VTS94CODESecret
```

#### b. Configuration VTP (Mode Serveur)

```cisco
vtp mode server
vtp domain TVS
vtp password VTSer78Sc0de
```

#### c. Création des VLANs

```cisco
vlan 10
 name DI
vlan 20
 name VM
vlan 30
 name SC
vlan 40
 name RH
vlan 50
 name FC
exit
```

#### d. Configuration LACP (Port-Channel)

```cisco
interface range GigabitEthernet0/1-2
 channel-group 1 mode active
 exit
interface Port-channel1
 switchport mode trunk
 exit
```

#### e. Affectation des ports d'accès

```cisco
interface FastEthernet0/1
 description Connexion_PC_Service_DI
 switchport mode access
 switchport access vlan 10
 exit
interface FastEthernet0/2
 description Connexion_PC_Service_VM
 switchport mode access
 switchport access vlan 20
 exit
interface FastEthernet0/3
 description Connexion_PC_Service_SC
 switchport mode access
 switchport access vlan 30
 exit
```

#### f. Configuration de l'accès SSH

```cisco
interface Vlan1
 ip address 192.168.1.253 255.255.255.0
 no shutdown
 exit
ip domain-name tvs.lan
crypto key generate rsa
 1024
line vty 0 15
 transport input ssh
 login local
 exit
end
copy running-config startup-config
```

### 2\. Configuration de `vega` (Client VTP)

#### a. Identité et Sécurité

```cisco
enable
configure terminal
hostname vega
enable secret VTS99SecretCODE
username admin privilege 15 secret VTS455CODESecret
```

#### b. Configuration VTP (Mode Client)

```cisco
vtp mode client
vtp domain TVS
vtp password VTSer78Sc0de
exit
```

#### c. Configuration LACP

```cisco
interface range GigabitEthernet0/1-2
 channel-group 1 mode active
 exit
interface Port-channel1
 switchport mode trunk
 exit
```

#### d. Affectation des ports d'accès

```cisco
interface FastEthernet0/4
 description Connexion_PC_Service_RH
 switchport mode access
 switchport access vlan 40
 exit
interface FastEthernet0/5
 description Connexion_PC_Service_FC
 switchport mode access
 switchport access vlan 50
 exit
```

#### e. Configuration de l'accès SSH

```cisco
interface Vlan1
 ip address 192.168.1.254 255.255.255.0
 no shutdown
 exit
ip domain-name tvs.lan
crypto key generate rsa
 1024
line vty 0 15
 transport input ssh
 login local
 exit
end
copy running-config startup-config
```

## 🧪 Vérifications et Tests

  * **Vérifier l'état du Port-Channel (LACP) sur les deux commutateurs :**
    `show etherchannel summary`

  * **Vérifier la synchronisation des VLANs via VTP :**
    `show vlan brief`

  * **Tester la connectivité entre deux PCs du même VLAN sur des commutateurs différents** (ex: ping de `di-pc-001` vers `di-pc-002`).

  * **Tester la connexion administrative sécurisée** depuis le `admin-pc-001` :
    `ssh admin@192.168.1.253`

✅ **La segmentation est fonctionnelle, la redondance est active et les commutateurs sont sécurisés.**
