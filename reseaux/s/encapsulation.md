# Encapsulation

**PDU :** Protocol Data Unit : Unité transportée entre chaque couches

**OSI :** Open-System-Intercommunications, sert à décomplexifier les communications, grâce à un découpage fort

**Couches TCP/IP** :

- Application : données brute
- Transport : TCP/UDP $\rightarrow$ Interprocessus de la machine
- Réseau : IP transport des packets
- Liaison : Ethernet (identification de l'@marc source et destination, et d'autres trucs)

**Encapsulation :** Processus par lequel les protocoles ajoutent leurs informations aux données

**PDU de chaque couches :**

| Osi Model      | PDU     |
| -------------- | ------- |
| 7 Application  | Data    |
| 6 Presentation | Data    |
| 5 Session      | Data    |
| 4 Transport    | Segment |
| 3 Network      | Packet  |
| 2 Data link    | Frame   |
| 1 Physical     | Bits    |

**Quels PDU circulent dans un réseau local ?** Packets & Segments

**Protocole :** Ensemble de règles qui fournissent des fonctionnalités

**Modèle utilisé par l'Internet :** Métro Ethernet
