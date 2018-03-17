# BLF

---
---

## TD3 Accès ADSL

---

### Questions de cours et application directe

#### Question 1

* PPP : Point to Point Protocol. Gère les sessions ADSL.
* DSLAM : Digital Subscriber Line Acess Multiplexer. Déssert les clients avec 1 paire torsadée par abonné, et une ou deux interfaces vers l'opérateur en fibre la plupart du temps.
* CPE : Customer Premises Equipments. Modem ADSL.
* BAS : Broadband Acess Server. Routeur entre les DSLAMs et le réseau Ip de l'opérateur, serveur PPP.
* AAL : ATM (Asynchronous Transfer Mode) Adaptation Layer. Adapte les trames ethernet par exemple pour le réseau ATM.
* SDH : Synchronous Digital Hierarchy. Sert à multiplexer soit des data soit de la voix pour transmettre sur des longues distances.

#### Question 2

Coté Lan|Interface Utilisateur
--|--
IP|IP
|
Ethernet|PPP
Ethernet|PPPOE
Ethernet|Ethernet
Ethernet|RFC 2684 (LLC)
Ethernet|AAL
|
Pysique|DSL

#### Question 3

Sans bourrage :

$$
AAL5 : 1410 Octets IP + 2 Octets PPP + 6 Octets PPPOE + 14 Octets Ethernet + 10 Octets RFC 2684 + 8 Octets AAL5 = 1450 Octets = 30 x 48 + 10 Octets
$$

Avec 38 octets de bourrage et on aura 31 celules ATM

La couche ATM ajoute 5 O d'en-tête par cellules soit en tout
31 x (48+5) = 1643 Octets
Débit au niveau IP (1410/1643) x 768 =  676 kb/s

#### Question 4

Dans le contexte du triple play (Voix, Tv, Internet) il est pertinent de définir des classes de services ADSL, pour par exemple donner la priorité à la voix.

Sur le réseau de collecte 3 options :
* ATM
* VLAN
* MPLS

Sur l'Interface Utilisateur :
* Tag de VLAN
* Circuits virtuels
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEyMTgxODI4NzNdfQ==
-->