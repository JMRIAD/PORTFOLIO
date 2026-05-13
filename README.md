# 🖧 Topologie Réseau — Gestion de Parc Informatique

> **Contexte** : Ce dépôt présente la configuration d'une topologie réseau d'entreprise avec segmentation VLAN, routage inter-VLAN (Router-on-a-Stick), et une analyse complète de la gestion du parc informatique via GLPI.

---

## 📋 Table des matières

1. [Présentation de la topologie](#-présentation-de-la-topologie)
2. [Configuration réseau](#-configuration-réseau)
3. [Rôle de GLPI dans ce scénario](#-rôle-de-glpi-dans-ce-scénario)
4. [Incidents potentiels](#-incidents-potentiels)
5. [Vulnérabilités et problèmes sous-jacents](#-vulnérabilités-et-problèmes-sous-jacents)
6. [Corrections et bonnes pratiques](#-corrections-et-bonnes-pratiques)
7. [Changements de configuration recommandés](#-changements-de-configuration-recommandés)
8. [Normes internationales appliquées](#-normes-internationales-appliquées)

---

## 🗺 Présentation de la topologie

### Schéma général

```
                        [Internet / WAN]
                               |
                           [Pare-feu]
                               |
                    [Routeur Principal R1]
                    (Router-on-a-Stick)
                               |
                    [Switch Core SW-CORE]
                    /          |          \
              [SW-ACC1]    [SW-ACC2]    [SW-ACC3]
                 |              |             |
           VLAN 10          VLAN 20        VLAN 30
        (Direction)     (Informatique)  (Utilisateurs)
```

> 💡 *Ajouter ici une capture d'écran de la topologie Packet Tracer*

### Plan d'adressage IP

| VLAN | Nom | Réseau | Passerelle | Plage utilisable |
|------|-----|--------|------------|------------------|
| 10 | Direction | 192.168.10.0/24 | 192.168.10.1 | .2 → .254 |
| 20 | Informatique | 192.168.20.0/24 | 192.168.20.1 | .2 → .254 |
| 30 | Utilisateurs | 192.168.30.0/24 | 192.168.30.1 | .2 → .254 |
| 99 | Management | 192.168.99.0/24 | 192.168.99.1 | .2 → .254 |

---

## ⚙️ Configuration réseau

### Routeur R1 — Router-on-a-Stick

```cisco
! Activation de l'interface physique
interface GigabitEthernet0/0
 no shutdown

! Sous-interface VLAN 10 (Direction)
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
 description VLAN_Direction

! Sous-interface VLAN 20 (Informatique)
interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
 description VLAN_Informatique

! Sous-interface VLAN 30 (Utilisateurs)
interface GigabitEthernet0/0.30
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.0
 description VLAN_Utilisateurs

! Sous-interface VLAN 99 (Management)
interface GigabitEthernet0/0.99
 encapsulation dot1Q 99 native
 ip address 192.168.99.1 255.255.255.0
 description VLAN_Management
```

### Switch Core SW-CORE — Configuration des VLANs

```cisco
! Création des VLANs
vlan 10
 name Direction
vlan 20
 name Informatique
vlan 30
 name Utilisateurs
vlan 99
 name Management

! Port trunk vers le routeur R1
interface GigabitEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,99
 switchport trunk native vlan 99
 description Trunk_vers_R1

! Port trunk vers les switches d'accès
interface GigabitEthernet0/2
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,99
 description Trunk_vers_SW-ACC1
```

### Switch d'accès SW-ACC1 — Ports utilisateurs

```cisco
! Ports accès VLAN 10 (Direction)
interface range FastEthernet0/1-5
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 spanning-tree bpduguard enable
 description Postes_Direction

! Port trunk vers SW-CORE
interface GigabitEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,99
 description Trunk_vers_SW-CORE
```

---

## 🛠 Rôle de GLPI dans ce scénario

### Qu'est-ce que GLPI ?

**GLPI** (Gestionnaire Libre de Parc Informatique) est un outil ITSM open-source permettant de gérer l'ensemble du patrimoine informatique d'une organisation : inventaire, helpdesk, gestion des changements, contrats, et supervision.

### Apport de GLPI dans cette topologie

#### 1. Inventaire automatisé du parc

Dans cette topologie à 3 VLANs, l'agent GLPI peut être déployé sur **chaque poste utilisateur** (VLAN 10, 20, 30) et remonter automatiquement :

- Le matériel : CPU, RAM, disques, carte réseau, adresse MAC
- Le logiciel : OS, applications installées, licences
- La localisation réseau : IP, VLAN d'appartenance, switch connecté

```
[Poste VLAN 10] ──agent──▶ [Serveur GLPI VLAN 20] ──▶ [Base de données]
[Poste VLAN 30] ──agent──▶         ↑
                         [Collecte via FusionInventory/Agent]
```

#### 2. Gestion des incidents (Helpdesk)

GLPI permet aux utilisateurs de **créer des tickets d'incidents** depuis leur navigateur. Dans ce scénario :

| VLAN source | Type d'incident typique | Priorité suggérée |
|-------------|------------------------|-------------------|
| VLAN 10 (Direction) | Accès VPN, messagerie | Haute |
| VLAN 20 (Informatique) | Panne serveur, mise à jour | Critique |
| VLAN 30 (Utilisateurs) | Imprimante, logiciel | Moyenne |

#### 3. Gestion des changements

Tout changement de configuration réseau (ajout d'un VLAN, modification d'ACL) peut être **tracé dans GLPI** via le module de gestion des changements, assurant une traçabilité conforme aux normes ITIL.

#### 4. Limites de GLPI dans ce contexte

- ❌ GLPI ne supervise pas en temps réel le réseau (pas de monitoring actif) → compléter avec **Zabbix**
- ❌ GLPI ne détecte pas automatiquement les intrusions → compléter avec un **IDS/IPS**
- ❌ L'agent GLPI nécessite une connectivité réseau entre les VLANs → nécessite des **ACLs permissives** vers le serveur GLPI

---

## 🚨 Incidents potentiels

### Incidents réseau

| ID | Incident | VLAN concerné | Impact | Probabilité |
|----|----------|--------------|--------|-------------|
| INC-01 | Boucle réseau (STP défaillant) | Tous | Critique — réseau down | Moyenne |
| INC-02 | VLAN Hopping (double encapsulation 802.1Q) | Tous | Critique — accès non autorisé | Faible |
| INC-03 | Saturation du lien trunk | SW-CORE ↔ R1 | Haute — dégradation service | Moyenne |
| INC-04 | Panne du routeur R1 (SPOF) | Tous | Critique — plus de routage inter-VLAN | Faible |
| INC-05 | Usurpation DHCP (Rogue DHCP Server) | VLAN 30 | Haute — mauvaise attribution d'IP | Moyenne |
| INC-06 | ARP Spoofing / Poisoning | Tous | Haute — interception de trafic | Moyenne |
| INC-07 | Accès non autorisé entre VLANs | VLAN 10 ↔ 30 | Haute — fuite de données | Faible |

### Incidents liés au parc informatique

| ID | Incident | Actif concerné | Impact |
|----|----------|---------------|--------|
| INC-08 | Poste utilisateur infecté par ransomware | VLAN 30 | Critique |
| INC-09 | Licence logicielle expirée | Tous postes | Moyenne |
| INC-10 | Disque dur défaillant sur serveur GLPI | Serveur VLAN 20 | Critique |
| INC-11 | Mot de passe administrateur compromis | Infrastructure | Critique |

---

## 🔓 Vulnérabilités et problèmes sous-jacents

### V1 — VLAN Natif non sécurisé

**Problème** : Si le VLAN natif du trunk est le VLAN 1 (défaut Cisco), un attaquant peut envoyer des trames sans tag et accéder à tous les VLANs.

```
Attaquant [VLAN 1] ──double tag 802.1Q──▶ trafic rebondit vers VLAN cible
```

**Impact** : Accès non autorisé à des VLANs sensibles (VLAN 10 Direction).

---

### V2 — Absence de DHCP Snooping

**Problème** : N'importe quel hôte du réseau peut se faire passer pour un serveur DHCP légitime et distribuer de fausses adresses IP / passerelles.

**Impact** : Redirection du trafic vers un hôte malveillant (attaque Man-in-the-Middle).

---

### V3 — Absence de Dynamic ARP Inspection (DAI)

**Problème** : Sans DAI, un attaquant peut envoyer de fausses réponses ARP et associer son adresse MAC à l'IP de la passerelle.

**Impact** : Interception de tout le trafic du segment réseau (ARP Poisoning).

---

### V4 — Ports inutilisés actifs sur les switches

**Problème** : Les ports non utilisés restent actifs et dans le VLAN par défaut (VLAN 1), permettant à un attaquant de brancher un équipement non autorisé.

**Impact** : Connexion physique non contrôlée au réseau.

---

### V5 — Absence d'authentification 802.1X

**Problème** : N'importe quel équipement branché physiquement sur un port switch accède immédiatement au réseau sans authentification.

**Impact** : Accès réseau non contrôlé.

---

### V6 — Point de défaillance unique (SPOF) sur R1

**Problème** : Le routeur R1 assure seul le routage inter-VLAN. Sa panne interrompt toute communication entre VLANs.

**Impact** : Indisponibilité totale des services inter-VLANs.

---

## ✅ Corrections et bonnes pratiques

### Correction V1 — Sécuriser le VLAN natif

```cisco
! Changer le VLAN natif vers un VLAN dédié inutilisé
interface GigabitEthernet0/1
 switchport trunk native vlan 99

! Désactiver le VLAN 1 sur tous les trunks
switchport trunk allowed vlan remove 1
```

### Correction V2 — Activer le DHCP Snooping

```cisco
! Activer globalement
ip dhcp snooping
ip dhcp snooping vlan 10,20,30

! Désactiver l'option 82 (évite les problèmes avec certains DHCP)
no ip dhcp snooping information option

! Marquer le port vers le DHCP légitime comme trusted
interface GigabitEthernet0/1
 ip dhcp snooping trust
```

### Correction V3 — Activer Dynamic ARP Inspection

```cisco
! Activer DAI sur les VLANs
ip arp inspection vlan 10,20,30

! Port trusted (vers le routeur / DHCP)
interface GigabitEthernet0/1
 ip arp inspection trust
```

### Correction V4 — Désactiver les ports inutilisés

```cisco
! Désactiver tous les ports inutilisés et les isoler dans un VLAN black-hole
interface range FastEthernet0/10-24
 switchport mode access
 switchport access vlan 99
 shutdown
 description PORT_INUTILISE
```

### Correction V5 — Activer PortFast + BPDU Guard sur les ports utilisateurs

```cisco
interface range FastEthernet0/1-9
 spanning-tree portfast
 spanning-tree bpduguard enable
```

> 💡 Pour une sécurité maximale, implémenter **802.1X** avec un serveur **RADIUS** (ex: FreeRADIUS) pour l'authentification des équipements.

### Correction V6 — Redondance avec HSRP

```cisco
! Routeur principal R1
interface GigabitEthernet0/0.10
 standby 10 ip 192.168.10.1
 standby 10 priority 110
 standby 10 preempt

! Routeur secondaire R2
interface GigabitEthernet0/0.10
 standby 10 ip 192.168.10.1
 standby 10 priority 100
```

---

## 🔧 Changements de configuration recommandés

| Priorité | Changement | Risque mitigé | Complexité |
|----------|-----------|--------------|------------|
| 🔴 Critique | Activer DHCP Snooping | Rogue DHCP | Faible |
| 🔴 Critique | Sécuriser le VLAN natif | VLAN Hopping | Faible |
| 🔴 Critique | Désactiver les ports inutilisés | Accès physique non autorisé | Faible |
| 🟠 Haute | Activer DAI | ARP Spoofing | Moyenne |
| 🟠 Haute | Implémenter HSRP sur R1/R2 | SPOF | Moyenne |
| 🟡 Moyenne | Déployer 802.1X + RADIUS | Accès non authentifié | Haute |
| 🟡 Moyenne | ACLs inter-VLANs | Mouvements latéraux | Moyenne |
| 🟢 Faible | Activer SSH v2 (désactiver Telnet) | Écoute réseau | Faible |

### ACLs inter-VLANs recommandées

```cisco
! Interdire au VLAN 30 (Utilisateurs) d'accéder au VLAN 10 (Direction)
ip access-list extended ACL_VLAN30_TO_VLAN10
 deny ip 192.168.30.0 0.0.0.255 192.168.10.0 0.0.0.255
 permit ip any any

interface GigabitEthernet0/0.30
 ip access-group ACL_VLAN30_TO_VLAN10 in
```

---

## 🌍 Normes internationales appliquées

### ISO/IEC 27001 — Sécurité de l'information

| Contrôle | Application dans cette topologie |
|---------|----------------------------------|
| A.9 — Contrôle d'accès | VLANs séparant les profils utilisateurs, ACLs inter-VLANs |
| A.12 — Sécurité opérationnelle | Journalisation des événements réseau, GLPI pour la traçabilité |
| A.13 — Sécurité des communications | Segmentation réseau, chiffrement des flux de management (SSH) |
| A.16 — Gestion des incidents | Processus de ticketing GLPI, classification des incidents |

### ITIL v4 — Gestion des services IT

| Pratique ITIL | Mise en œuvre |
|--------------|---------------|
| Gestion des incidents | Tickets GLPI avec SLA par VLAN/profil |
| Gestion des changements | Traçabilité des modifs réseau dans GLPI |
| Gestion des actifs | Inventaire automatisé via agent GLPI |
| Gestion des problèmes | Analyse des incidents récurrents dans GLPI |

### IEEE 802.1Q — Standard VLANs

- Encapsulation dot1Q sur les liens trunk
- Segmentation logique du réseau en 4 VLANs distincts
- VLAN natif dédié (VLAN 99) conformément aux bonnes pratiques

### IEEE 802.1D / 802.1w — Spanning Tree Protocol

- STP activé pour prévenir les boucles réseau
- RSTP (802.1w) recommandé pour une convergence plus rapide
- PortFast + BPDU Guard sur les ports utilisateurs finaux

### RGPD — Règlement Général sur la Protection des Données

| Obligation RGPD | Mesure technique appliquée |
|----------------|---------------------------|
| Minimisation des données | Accès segmenté par VLAN selon le rôle |
| Traçabilité | Journaux GLPI horodatés |
| Intégrité | ACLs limitant les accès inter-VLANs |
| Disponibilité | Redondance HSRP sur le routeur |

---

## 📚 Ressources et outils utilisés

- **Cisco Packet Tracer** — Simulation de la topologie réseau
- **GLPI** — Gestion du parc et helpdesk (ITSM)
- **Zabbix** — Supervision réseau en temps réel
- **Docker** — Conteneurisation du service GLPI

---

## 👤 Auteur

TIAKRAY MANGLE Jean Marc Riad  
Formation : Gestion du Parc Informatique et Incident  
Formateur : Boris Rose  

---

*Ce dépôt s'inscrit dans le cadre du portfolio obligatoire de la formation.*
