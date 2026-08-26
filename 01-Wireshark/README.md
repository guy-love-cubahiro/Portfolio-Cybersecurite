# Projet 01 – Analyse du trafic réseau avec Wireshark

## Présentation

Ce projet a été réalisé dans le cadre de mon portfolio en cybersécurité afin de développer des compétences pratiques en analyse réseau et en investigation SOC avec Wireshark.

Le laboratoire commence par l’analyse d’un échange ICMP entre deux machines virtuelles, puis évolue vers une investigation Blue Team d’une activité TCP inhabituelle afin de distinguer trafic normal, anomalie réseau et comportement de reconnaissance.

## Objectifs

- Capturer et filtrer du trafic réseau avec Wireshark.
- Analyser Ethernet II, IPv4, ICMP et TCP.
- Interpréter les principaux champs et flags réseau.
- Effectuer le triage d’une capture PCAP.
- Identifier et qualifier une activité réseau suspecte.
- Corréler plusieurs événements réseau.
- Construire une chronologie d’incident.
- Distinguer faits, observations, hypothèses et compromission confirmée.
- Associer un comportement observé à MITRE ATT&CK.
- Produire des conclusions et recommandations adaptées à un analyste SOC junior.

## Environnement du laboratoire

**Machine hôte**
- Windows 11

**Hyperviseur**
- VMware Workstation Pro 17

**Machines virtuelles**
- Windows 11 — `192.168.230.218`
- Kali Linux — `192.168.230.219`
- Windows Server 2022 — `192.168.230.220`

**Réseau**
- VMware NAT / VMnet8
- Sous-réseau : `192.168.230.0/24`

## Outils utilisés

- Wireshark
- VMware Workstation Pro
- Nmap, uniquement pour générer l’activité contrôlée du scénario SOC
- Git
- GitHub
- GitHub Desktop

## Partie 1 — Analyse ICMP

La première partie du projet porte sur un échange ICMP généré par un `ping` entre Windows 11 (`192.168.230.218`) et Windows Server 2022 (`192.168.230.220`).

L’analyse a permis d’étudier :

- Ethernet II ;
- IPv4 ;
- ICMP Echo Request et Echo Reply ;
- TTL ;
- adresses IP et MAC ;
- Identifier et Sequence Number ;
- temps de réponse ;
- connectivité entre les deux systèmes.

Le rapport technique contient l’analyse détaillée de cette partie.

## Partie 2 — Extension SOC / Blue Team

### Scénario

Une activité réseau inhabituelle provenant de `192.168.230.219` et ciblant `192.168.230.220` a été capturée puis analysée comme si l’analyste ne connaissait pas à l’avance l’outil ayant généré le trafic.

L’objectif était de déterminer la nature de l’activité uniquement à partir des preuves présentes dans le PCAP.

### Résultats clés

L’investigation a mis en évidence :

- environ **2 000 paquets TCP SYN** ;
- une fenêtre d’activité de **4,6284563 secondes** ;
- environ **432 SYN par seconde en moyenne** ;
- un grand nombre de ports TCP ciblés sur une même machine ;
- environ **1 988 conversations TCP filtrées** ;
- plusieurs réponses SYN/ACK indiquant des services TCP accessibles ;
- des services tels que SMB, RDP, LDAP, LDAPS et WinRM ;
- des connexions très courtes et répétitives ;
- un comportement fortement compatible avec une découverte automatisée de services.

### Preuves principales

![Multiples SYN vers différents ports](captures/10-syn-multiples-ports.png)

*Un même hôte génère un grand nombre de tentatives TCP SYN vers de nombreux ports de la même cible.*

![Services TCP accessibles](captures/11-synack-ports-accessibles.png)

*Plusieurs ports répondent par SYN/ACK, indiquant que des services TCP sont accessibles au moment de la capture.*

![Corrélation TCP sur le port 445](captures/13-correlation-tcp-port-445.png)

*La séquence SYN → SYN/ACK → ACK → RST/ACK montre une connexion TCP établie puis rapidement interrompue sur SMB.*

![Chronologie des services réseau](captures/14-chronologie-services-reseau.png)

*Plusieurs services sont testés sur une courte période, ce qui renforce l’hypothèse d’une reconnaissance automatisée.*

## MITRE ATT&CK

Le comportement observé est associé à :

- **Tactique : Discovery — TA0007**
- **Technique : Network Service Scanning — T1046**
- **Niveau de confiance : élevé**

Cette association décrit le comportement réseau observé. Elle ne prouve pas à elle seule une intention malveillante.

## Verdict SOC

**Classification :** activité suspecte de reconnaissance / découverte de services TCP.

**Sévérité proposée :** moyenne.

**Compromission confirmée :** non.

**Exploitation confirmée :** non.

L’activité serait à investiguer davantage dans un environnement réel afin de déterminer si la source était autorisée à effectuer ce type de reconnaissance.

## Compétences développées

- Capture et analyse de trafic réseau avec Wireshark
- Analyse Ethernet II, IPv4, ICMP et TCP
- Filtres d’affichage Wireshark
- Analyse des flags TCP
- Triage de captures PCAP
- Identification de trafic inhabituel et suspect
- Analyse de reconnaissance réseau
- Corrélation d’événements
- Construction d’une chronologie d’incident
- Qualification d’indicateurs réseau
- Distinction entre IOC, anomalie et indicateur comportemental
- Analyse Blue Team
- Mapping MITRE ATT&CK
- Évaluation de la sévérité et du niveau de confiance
- Documentation technique d’une investigation SOC
- Git et GitHub

## Structure du projet

```text
01-Wireshark/
│
├── captures/
├── captures-pcap/
├── rapports/
└── README.md
```

## Fichiers principaux

- `captures-pcap/01-ping-windows11-vers-server.pcapng`
- `captures-pcap/02-investigation-soc-scan-reseau.pcapng`
- `rapports/01-analyse-icmp.md`

## État du projet

✅ Projet terminé et enrichi avec une investigation réseau orientée SOC / Blue Team.

## Auteur

**Guy Love Cubahiro**

Portfolio réalisé dans le cadre de ma préparation à un premier poste d’analyste cybersécurité / SOC junior.
