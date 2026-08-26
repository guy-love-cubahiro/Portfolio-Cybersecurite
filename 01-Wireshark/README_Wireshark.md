# Projet 01 – Analyse du trafic réseau avec Wireshark

## Présentation

Ce projet a été réalisé dans le cadre de la création de mon portfolio en cybersécurité.

L'objectif est de développer une maîtrise pratique de Wireshark, d'abord pour comprendre les principaux protocoles réseau, puis pour appliquer ces connaissances à une investigation orientée Blue Team / SOC.

Le projet repose sur un laboratoire personnel exécuté sous VMware Workstation Pro et composé d'une machine hôte Windows 11 ainsi que de machines virtuelles Windows 11, Windows Server 2022 et Kali Linux.

---

## Objectifs

- Capturer du trafic réseau avec Wireshark.
- Sélectionner l'interface de capture adaptée à un environnement VMware.
- Utiliser des filtres d'affichage pour isoler le trafic pertinent.
- Analyser les protocoles Ethernet II, IPv4, ICMP et TCP.
- Interpréter un échange ICMP Echo Request / Echo Reply.
- Lire les principaux champs d'un en-tête IPv4.
- Identifier les flags et comportements TCP utiles à une investigation.
- Distinguer trafic normal, inhabituel et potentiellement suspect.
- Identifier et qualifier des indicateurs réseau.
- Construire une chronologie d'incident à partir des timestamps réseau.
- Corréler plusieurs événements réseau.
- Associer un comportement observé à MITRE ATT&CK.
- Formuler une conclusion et des recommandations Blue Team sans dépasser ce que les preuves permettent d'affirmer.
- Documenter l'ensemble de l'analyse de manière professionnelle sur GitHub.

---

## Environnement du laboratoire

**Machine hôte**
- Windows 11
- Wireshark

**Hyperviseur**
- VMware Workstation Pro 17

**Machines virtuelles**
- Windows 11 — `192.168.230.218`
- Kali Linux — `192.168.230.219`
- Windows Server 2022 — `192.168.230.220`

**Réseau utilisé**
- VMware NAT — VMnet8
- Sous-réseau : `192.168.230.0/24`

---

## Outils utilisés

- Wireshark
- VMware Workstation Pro
- Windows 11
- Windows Server 2022
- Kali Linux
- Nmap, uniquement comme générateur contrôlé de trafic dans le scénario SOC
- Git
- GitHub
- GitHub Desktop

---

## Laboratoires réalisés

- ✅ Capture et analyse ICMP (Ping)
- ✅ Analyse Ethernet II et IPv4
- ✅ Analyse ICMP Echo Request / Echo Reply
- ✅ Analyse des principaux champs IPv4 : IHL, DSCP, Identification, Flags, Fragment Offset, TTL, Protocol, Checksum, Source et Destination
- ✅ Analyse des champs ICMP : Type, Code, Checksum, Identifier, Sequence Number et temps de réponse
- ✅ Investigation SOC d'une activité réseau inhabituelle
- ✅ Analyse des flags TCP et des ports ciblés
- ✅ Corrélation de plusieurs connexions vers des services Windows / Active Directory
- ✅ Construction d'une chronologie réseau
- ✅ Qualification des indicateurs réseau
- ✅ Mapping MITRE ATT&CK
- ✅ Conclusion et recommandations Blue Team

---

## 1. Analyse ICMP — trafic normal de référence

Le premier laboratoire a servi à établir une base de compréhension du trafic réseau normal entre le poste Windows 11 et Windows Server 2022.

La commande suivante a généré le trafic :

```cmd
ping 192.168.230.220
```

Le filtre d'affichage utilisé dans Wireshark était :

```text
icmp
```

### Interface VMnet8

![Interface VMnet8](captures/01-interface-vmnet8.png)

### Capture et filtrage ICMP

![Capture ICMP](captures/02-capture-icmp.png)

![Filtre ICMP](captures/03-filtre-icmp.png.png)

### Analyse du paquet et de l'en-tête IPv4

![Détails du paquet ICMP](captures/04-details-paquet-icmp.png)

![Détails de l'en-tête IPv4](captures/05-details-entete-ipv4.png.png)

### Echo Request et Echo Reply

![ICMP Echo Request](captures/06-icmp-echo-request.png)

![ICMP Echo Reply](captures/07-icmp-echo-reply.png)

Cette première partie a permis de confirmer une communication ICMP normale entre `192.168.230.218` et `192.168.230.220`, avec une requête Echo Request (Type 8), une réponse Echo Reply (Type 0), des identifiants et numéros de séquence corrélables, ainsi qu'un temps de réponse très faible dans le réseau virtuel.

---

## 2. Extension SOC — Investigation d'une activité de reconnaissance réseau

### Scénario

Une activité réseau inhabituelle provenant de `192.168.230.219` et ciblant `192.168.230.220` a été analysée à partir d'une nouvelle capture PCAP.

L'objectif était de déterminer la nature de cette activité **à partir des preuves réseau**, sans supposer à l'avance l'outil responsable.

### Triage initial

L'analyse de la hiérarchie des protocoles a montré une capture dominée par IPv4 et TCP, avec également du trafic UDP, QUIC, TLS, DNS, LDAP, ARP et HTTP. Le triage a ensuite isolé une relation particulièrement importante entre `192.168.230.219` et `192.168.230.220`.

![Hiérarchie des protocoles](captures/08-hierarchie-protocoles-investigation.png)

![Conversations IPv4](captures/09-conversations-ipv4-investigation.png)

### Identification du comportement suspect

Le filtrage des paquets TCP SYN initiés par `192.168.230.219` vers `192.168.230.220` a mis en évidence environ **2 000 tentatives SYN** vers un grand nombre de ports.

![Multiples SYN vers différents ports](captures/10-syn-multiples-ports.png)

Les réponses SYN/ACK du serveur ont permis d'identifier plusieurs services TCP accessibles au moment de la capture.

![Services répondant par SYN ACK](captures/11-synack-ports-accessibles.png)

La vue des conversations TCP montre un très grand nombre de petites conversations vers des ports différents de la même cible, ce qui renforce l'hypothèse d'une activité automatisée de découverte de services.

![Conversations TCP multiples](captures/12-conversations-tcp-multiples-ports.png)

### Corrélation TCP

Sur le port 445/TCP, la séquence observée est :

1. SYN envoyé par `192.168.230.219` ;
2. SYN/ACK retourné par `192.168.230.220` ;
3. ACK envoyé par la source ;
4. RST/ACK envoyé rapidement par la source.

![Corrélation TCP port 445](captures/13-correlation-tcp-port-445.png)

Ce pattern montre qu'une connexion TCP a été établie puis rapidement interrompue, ce qui est compatible avec un test d'accessibilité de service plutôt qu'avec une longue session applicative.

### Chronologie réseau

Le premier SYN de la séquence étudiée est observé au paquet `2036` :

`2026-08-26 00:24:36,884959600` — `192.168.230.219 → 192.168.230.220:22/TCP`

Le dernier SYN est observé au paquet `4079` :

`2026-08-26 00:24:41,513415900` — `192.168.230.219 → 192.168.230.220:2008/TCP`

La fenêtre d'activité est donc d'environ **4,63 secondes**, soit environ **432 SYN par seconde en moyenne**.

![Chronologie des services réseau](captures/14-chronologie-services-reseau.png)

Plusieurs services ont répondu positivement pendant cette fenêtre, notamment SMB, RDP, LDAP, LDAPS et WinRM.

---

## 3. Qualification Blue Team

### Faits établis

- Source observée : `192.168.230.219`
- Cible : `192.168.230.220`
- Environ 2 000 paquets SYN vers de nombreux ports TCP
- Activité concentrée sur environ 4,63 secondes
- Environ 432 SYN/s en moyenne
- Plusieurs réponses SYN/ACK
- De nombreuses conversations TCP très courtes

### Indicateurs réseau

Les adresses IP privées du laboratoire ne constituent pas, à elles seules, des IOC de compromission.

L'investigation a cependant identifié plusieurs **indicateurs comportementaux suspects** :

- volume élevé de SYN ;
- grand nombre de ports ciblés ;
- forte concentration temporelle ;
- nombreuses conversations TCP de très courte durée ;
- répétition du même comportement sur plusieurs services ;
- identification de services accessibles sur une cible Windows / Active Directory.

### Distinction entre observation et compromission

Le PCAP démontre une activité fortement compatible avec de la reconnaissance automatisée de services. En revanche, il **ne démontre pas** :

- l'exploitation d'un service ;
- une authentification malveillante ;
- une compromission du serveur ;
- la présence d'un malware ;
- l'identité certaine de l'outil responsable.

---

## 4. MITRE ATT&CK

Le comportement observé a été associé à :

- **Tactique :** Discovery — `TA0007`
- **Technique :** Network Service Scanning — `T1046`

**Niveau de confiance : élevé.**

Le mapping est justifié par le grand nombre de ports testés, l'activité extrêmement concentrée dans le temps, la répétition des tentatives et l'identification de plusieurs services accessibles.

---

## 5. Sévérité et recommandations SOC

**Sévérité proposée : Moyenne**

Cette classification tient compte du fait que l'activité de reconnaissance peut fournir des informations utiles sur la surface d'attaque de la cible, mais qu'aucune exploitation ou compromission n'est démontrée.

Dans un environnement de production, les actions recommandées seraient notamment :

1. vérifier si la source est autorisée à effectuer des scans réseau ;
2. identifier l'utilisateur et le processus à l'origine des connexions ;
3. rechercher si la même source a ciblé d'autres systèmes ;
4. examiner les journaux Windows et les données EDR de la source et de la cible ;
5. rechercher des authentifications ou connexions ultérieures sur les services découverts ;
6. corréler avec les journaux du pare-feu, IDS/IPS et SIEM ;
7. limiter l'accès à RDP, SMB, WinRM et aux services d'administration aux systèmes réellement nécessaires ;
8. appliquer la segmentation réseau et le principe du moindre privilège réseau ;
9. créer ou ajuster des règles de détection pour les scans de ports anormalement rapides ;
10. escalader l'incident si une activité postérieure à la reconnaissance est identifiée.

---

## Compétences développées

- Capture et analyse de trafic réseau
- Analyse des protocoles Ethernet II, IPv4, ICMP et TCP
- Utilisation de filtres d'affichage Wireshark
- Analyse des flags et connexions TCP
- Triage d'une capture réseau
- Identification de trafic potentiellement suspect
- Distinction entre trafic normal, inhabituel et suspect
- Analyse de reconnaissance et de découverte de services
- Qualification d'indicateurs réseau
- Corrélation d'événements réseau
- Construction d'une chronologie d'incident
- Analyse Blue Team
- Mapping MITRE ATT&CK
- Évaluation de la sévérité et du niveau de confiance
- Documentation technique d'une investigation SOC
- Gestion de projet avec Git et GitHub

---

## Structure du projet

```text
01-Wireshark/
│
├── captures/
├── captures-pcap/
├── rapports/
└── README.md
```

Les fichiers PCAP principaux sont :

- `01-ping-windows11-vers-server.pcapng` — analyse ICMP initiale ;
- `02-investigation-soc-scan-reseau.pcapng` — investigation SOC de reconnaissance réseau.

---

## Résultat

Ce projet démontre ma capacité à :

- capturer et filtrer du trafic réseau avec Wireshark ;
- analyser un échange ICMP de bout en bout ;
- interpréter les principaux champs Ethernet II, IPv4, ICMP et TCP ;
- mener le triage d'une capture réseau ;
- identifier une activité réseau potentiellement suspecte ;
- corréler des tentatives et réponses TCP ;
- construire une chronologie d'incident ;
- distinguer un indicateur comportemental d'un IOC de compromission confirmé ;
- mapper une observation pertinente à MITRE ATT&CK ;
- évaluer la sévérité et le niveau de confiance ;
- formuler une conclusion et des recommandations Blue Team prudentes et argumentées ;
- documenter l'analyse de manière professionnelle sur GitHub.

---

## État du projet

✅ Projet terminé 

---

## Auteur

**Guy Love Cubahiro**

Portfolio réalisé dans le cadre de ma préparation à un poste d'analyste cybersécurité junior et/ou d'analyste SOC junior.
