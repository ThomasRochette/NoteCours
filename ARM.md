# ARM

---
---
## Sécurité des Réseaux Céllulaires
---

### Notions de base

* Authentification : vérifier la légitimité d'Accès
* Confidentialité : données chiffrées
* Intégrité : information non altérée

Les clés symétriques nécessitent plus de ressources pour être cassées et sont beaucoup plus rapide, le problème du chiffrement symétriqque est la distribution des clés.

Il y a 2 type de chiffrement symétrique :
* Par bloc, chiffrement symétrique classique
* Par flot, on génère une suite de nombre aléatoire puis on fait un ou exclusif avec les données, ce chiffrement est plus rapide mais plus vulnérable au attaques statistiques

### GSM

EIR : Registre qui contient la liste des IMEI (liste des mobiles)

Principale vulnérabilité à cause des réseau sans fils. Plusieurs besoin :
* Confidentialité de l'IMSI
* Authentification
* Confidentialité des données
* Intégrité

En GSM on assure :
* La gestion des équipement volés
* Identité temporaire (TMSI) à la place de l'IMSI
* **Authentification**
* **Chiffrement**

#### Authentification

* la carte sim nous identifie avec une clé primaire unique (Ki = 128 bits) qui ne sort jamais de la sim. On retrouve cette clé dans le AUC associé au IMSI.
* Le mobile envoie son IMSI ou TMSI s'il en a un
* Le MSCS/VLR fait la correspondance TMSI/IMSI il demande au mobile de répondre à un problème avec sa clé Ki. On génère un RAND qu'on "hach" on obtient XRES avec cette valeur et la clé Ki on calcul une clé Kc on envoie le RAND jusqu'à la carte sim qui va calculer un SRES et recalculé un Kc avec sa clé Ki si l'utilisateur est authentique les Kc sont les mêmes.
* La clé Kc est utiliser ensuite comme clé symétrique

L'authentification est régulierement déclanché par le réseau.

#### Limites

* Les transmission sont seulement crypté entre MS et BTS 
* L'authentification est faite dans un seul sens (BTS malvaillante)
* GSM n'assure pas l'intégrité des messages
* Les algorithmes de chiffrement (A5/1 et A5/2) sont faibles brute force de 2^64 facile avec les moyens d'aujourd'hui (permet d'avoir la clé de session uniquement)

### UMTS

* Authentification mutuel
* Intégrité des messages
* Chiffrement
* Meilleur algorithme pour la confidentialité et l'intégrité

---
---
## LTE (Long Term Evolution)
---
![
](https://lh3.googleusercontent.com/xPUjO6-KE8JmoiIOz1uNl6KceB-WDwkXW_D90Y0UfWm76FbDB_52AqQcLcK4Q7Yp7I5yFD2bAFs8 "LTE")
Sur le réseau d'accès, il n'y a plus qu'un seul équipement, l'eNodeB. Les eNB sont connectés au cœur de réseau via les S1, le contrôle est envoyé au MME, les paquets ip sont envoyés au S-GW( serving gateway). Les eNB sont connectés entre elles via des liens X2, ces liens servent à gérer le handover.
![enter image description here](https://lh3.googleusercontent.com/h3R4v9-L3eXe09XYHKSLJrnUumt_JxrQ0fJnCMfUModyPoX5VvULDqACaihZckNsdZcpAe9ym5ys)


### eNodeB
Possède les fonctionnalités classique du réseau d'accès :
* La gestion des ressources radio
	* Contrôle d'admission
	* Contrôle de la mobilité
	* Ordonnancement et allocation des ressources radio
* Compression des en-tête
* Sécurité, authentification auprès du MME mais le reste (chiffrement déchiffrement) se fait à l'eNB
* Connectivité
* Positionnement, avec le timing advance de l'utilisateur on connais la distance de l'utilisateur par rapport à une station de base, avec 3 timing advance on connais la position de l'utilisateur.

### Coeur de réseau
![enter image description here](https://lh3.googleusercontent.com/_TywqtzUSIDowRaShPnm2TrcfyhgFCO3wgGphnQM6YMIgk643G3qhBydEjU5Ct0Yr-KnZwYC1eM-)
#### Packet Gateway (P-GW)
2 fonctionnalités importantes :
* Fin du tunnel IP, le bearer est établi entre le mobile et le packet gateway
* Fait tourner un serveur DHCP pour donner les IP aux utilisateurs
Il fait aussi du filtrage au niveau des paquets (débats quant au non respect de la neutralité du web). C'est le dernier équipement IP de l'opérateur, il permet de sortir vers "l’extérieur", c'est sur cet équipement qu'on place la QoS.
#### Serving Gateway (S-GW)
2 rôles important :
* Compte la consommation de data car le standard n'interdit pas les communications internes passant de l'utilisateur A vers l'eNB puis vers la S-GW pour retourner vers l'eNB et enfin l'utilisateur B sans passer par la P-GW (Problématique sur le comptage : est ce que les en-tête sont compté dans la data, etc).
* Mise en file d'attente des paquets quand l'utilisateur est en mode IDLE 
C'est Ici que l'opérateur peut observer nos communications. Lors du handover, le bearer est reconfiguré, à ce moment la QoS ne peut pas être garantie jusqu'à l'utilisateur, s'il n'y a pas de changement de S-GW on reconfigure uniquement entre l'eNB et le S-GW sinon on reconfigure tout.
#### Mobility Managment Entity (MME)
Sert à l'authentification, gère les zone de localisation, fait le paging, choisit le S-GW/P-GW, gère les bearer
#### Home Subscriber Server (HLR/HSS)
Stock les données utilisateurs (abonnement, applications payantes, roaming, etc). Mise à jour de la zone de localisation, identité du MME. Centre d'authentification.
#### Policy Control and Charging Rules Fonction (PCRF)
Etat de la facture. Donnes les droits d'accès au applications gratuite, etc.
#### Enhanced Serving Mobile Location Center (E-SMLC)
Coordonne le processus de localisation par triangulation. Choisit les ressource à utiliser, calcul la position et la vitesse.(**Gateway Mobile Location Center (GMLC)** autre équipement aussi utilisé)
#### Etapes de la Connexion
1. Connection RRC : UE - eNB
2. Choix du MME (par l'eNB) et mise en place du bearer de signalisation
3. Authentification et attachement avec le HSS
4. Sélection P-GW et S-GW grace au MME
5. Mise en place du bearer et allocation de l'adresse IP par la P-GW
6. Reconfiguration du RRC 
```mermaid
sequenceDiagram
UE -> eNB: Connection RRC
eNB -> MME: Choix du MME et mise en place du bearer de signalisation
MME -> HSS: Authentification et attachement
MME -> SGW/PGW: Sélection
SGW/PGW -> UE: Mise en place du bearer et allocation @IP
```

---
---
## Annalyse des donneés mobiles
---
Les opérateurs observent le réseau avec des probe pour pouvoir faire de la classification, de la prédiction et de la détection d'anomalies. Ils'utilisent aussi leurs application de gestion de compte pour récolter des données notamment au delà de ce qui concerne le réseau de l'opérateur. Les opérateurs achetant leur matériel RAN chez des constructeurs comme Nokia, Ericsson, Huawei, les métriques ne sont pas toujours les mêmes ils ont donc des spécialistes sur chaque technologies.

---
---
## TP LTE
---
#### Question 1.1 :
Les algorithmes supportés sont listés dans le message ATTACH_REQUEST, les algorithmes sont :
* EEA0 (encryption)
* 128-EEA1 (encryption)
* 128-EEA2 (encryption)
* EIA0 (integrity)
* 128-EIA1 (integrity)
* 128-EIA2 (integrity)

#### Question 1.2 :
Les vecteurs RAND et AUTN sont transmis avec le message AUTHENTICATION_REQUEST

#### Question 1.3 :
```mermaid
sequenceDiagram
participant USIM
 
UE ->> MME: (IMSI)
MME ->> HSS: Authentication info (IMSI)
HSS ->> HSS: Generate EPS AV incl SN id
HSS ->> MME: Authentication info answer (RAND, XRES, Kasme, AUTN)
MME ->> UE: Authentication request (RAND, AUTN)
UE ->> USIM: 
USIM ->> USIM: Verify AUTN, Compute RES, Compute CK and IK
USIM ->> UE: 
UE ->> MME: Authentication response (RES)
MME ->> MME: Compare RES and XRES
UE ->> UE: Compute Kasme incl SN id
```

#### Question 1.4-1.6 :
L'UE choisi l'antenne à laquelle il s'associe car c'est un scénario ou l'UE arrive dans le réseau (démarrage), il choisi l'antenne qui lui fournis le meilleur signal.

#### Question 1.7 :
```mermaid
sequenceDiagram
UE ->> eNB: RRC Connection Request
eNB ->> UE: RRC Connection Setup
UE ->> eNB: RRC Connection Setup Complete
eNB ->> MME: Initial UE Message
MME ->> eNB: Initial Context Setup Request
eNB ->> UE: RRC Connection Reconfigration 
UE ->> eNB: RRC Connection Reconfiguration Complete
eNB ->> MME: Initial Context Setup Response
```

#### Question 1.8 :
L'UE peut utiliser 2 identifiant pour le message RRC CONNECTION REQUEST, soit le TMSI s'il est toujours dans la même zone de localisation puisque les TMSI sont unique par zone de localisation, soit il utilise un nombre aléatoire sur 40 bits (contre 32 pour le TMSI) avant d'avoir un nouveau TMSI sur la zone de localisation.

#### Question 1.9 :
Avec le MME on utilise l'IMSI au lieu du TMSI.

#### Question 1.10 :
```mermaid
graph LR
A(Idle) --> B(Connected)
B --> C(Short DRX)
C --> B
C --> D(Long DRX)
D --> A
D --> B
```

#### Question 1.11-1.13 :
Il faut un bearer, ces ressources sont allouées grâce au ACTIVATE DEFAULT EPS BEARER CONTEXT ACCEPT le numéro de bearer est 5 le type est Non-GBR et le type de QoS est QCI6

#### Question 2.1 :
```mermaid
sequenceDiagram
UE -> Source eNB: Measurment
Source eNB -> Source eNB: Handover Decision
Source eNB ->> Target eNB: Handover Request
Target eNB -> Target eNB: Admission Control
Target eNB ->> Source eNB: Handover Response
Source eNB ->> UE: RRC Reconfig (Handover Command)
UE -> UE: Detach from old cell, Sychro with new cell
UE ->> Target eNB: RRC Reconfig
Target eNB -->> UE: SBR and RB re-establishment
```
#### Question 2.2 :
Passer de LTE à UMTS pour un appel correspond à du fall back pas à du handover.

#### Question 3.1 :
100Mbps
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTQyMjY4OTU3MV19
-->