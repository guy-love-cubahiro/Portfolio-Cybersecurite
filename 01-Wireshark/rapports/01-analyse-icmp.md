# Rapport technique — Projet 01 : Analyse réseau avec Wireshark

## 1. Objectif

Ce rapport documente deux volets complémentaires du Projet 01 :

1. l’analyse fondamentale d’un échange ICMP avec Wireshark ;
2. une extension orientée SOC / Blue Team consacrée à l’investigation d’une activité TCP inhabituelle.

L’objectif est de démontrer non seulement la capacité à lire des paquets réseau, mais également à conduire une investigation structurée, corréler des événements, construire une chronologie et produire une conclusion prudente fondée sur les preuves.

---

## 2. Environnement

### Machine hôte
- Windows 11
- Wireshark
- VMware Workstation Pro

### Machines virtuelles
- Windows 11 — `192.168.230.218`
- Kali Linux — `192.168.230.219`
- Windows Server 2022 — `192.168.230.220`

### Réseau
Le trafic est observé sur l’interface virtuelle **VMnet8**, dans le réseau NAT VMware `192.168.230.0/24`.

---

# Partie I — Analyse ICMP

## 3. Scénario ICMP

Un `ping` est envoyé depuis Windows 11 (`192.168.230.218`) vers Windows Server 2022 (`192.168.230.220`).

Commande utilisée :

```cmd
ping 192.168.230.220
```

Filtre Wireshark :

```text
icmp
```

La capture permet d’observer les requêtes **ICMP Echo Request** et les réponses **ICMP Echo Reply** correspondantes.

## 4. Analyse Ethernet II

L’en-tête Ethernet II permet d’identifier les adresses MAC source et destination utilisées au niveau Liaison de données.

Cette analyse permet de distinguer l’adressage matériel local de l’adressage logique IPv4.

## 5. Analyse IPv4

Les principaux champs observés sont :

| Champ | Valeur / observation |
|---|---|
| Version | IPv4 |
| Longueur d’en-tête | 20 octets |
| DSCP | CS0 |
| TTL | 128 |
| Protocole | ICMP |
| Source | `192.168.230.218` |
| Destination | `192.168.230.220` |
| Fragmentation | non observée |
| Fragment Offset | 0 |

Le TTL de 128 correspond à la valeur présente dans le paquet observé. Le TTL est décrémenté lors du passage par des équipements de routage et permet d’éviter qu’un paquet circule indéfiniment.

Le checksum IPv4 était présent ; son interprétation dans une capture locale doit tenir compte des mécanismes de checksum offloading.

## 6. Analyse ICMP

L’échange suit le modèle :

```text
192.168.230.218                     192.168.230.220
       |                                    |
       | -------- ICMP Echo Request ------> |
       | <--------- ICMP Echo Reply ------- |
```

L’Echo Request utilise le **Type 8** et l’Echo Reply le **Type 0**.

Les champs Identifier et Sequence Number permettent d’associer la requête et la réponse correspondantes.

Un temps de réponse d’environ **0,336 ms** a été observé, cohérent avec une communication au sein du réseau virtuel local.

## 7. Conclusion de l’analyse ICMP

La communication ICMP observée est normale : les deux systèmes communiquent correctement et les requêtes reçoivent leurs réponses.

Cette première partie fournit la baseline nécessaire pour distinguer ultérieurement un échange réseau simple et attendu d’une activité beaucoup plus intensive et inhabituelle.

---

# Partie II — Investigation réseau SOC / Blue Team

## 8. Contexte de l’investigation

Une activité TCP inhabituelle provenant de :

```text
192.168.230.219
```

et ciblant :

```text
192.168.230.220
```

a été capturée dans :

```text
02-investigation-soc-scan-reseau.pcapng
```

Le scénario a été analysé du point de vue d’un analyste SOC recevant une capture réseau sans utiliser comme preuve la connaissance préalable de l’outil ayant généré l’activité.

### Questions d’investigation

L’analyse devait déterminer :

- quelle machine initiait l’activité ;
- quelle machine était ciblée ;
- quels protocoles et ports étaient impliqués ;
- la durée et l’intensité de l’activité ;
- si plusieurs événements pouvaient être corrélés ;
- si le comportement correspondait à du trafic normal ou à une activité suspecte ;
- quels indicateurs pouvaient être documentés ;
- quel mapping MITRE ATT&CK était justifié ;
- si une exploitation ou une compromission pouvait être démontrée.

---

## 9. Baseline et distinction trafic normal / suspect

L’analyse ICMP précédente constitue un exemple de trafic attendu : quelques requêtes vers une destination précise, suivies de réponses correspondantes.

Dans l’investigation SOC, l’activité présente un profil différent :

- grand nombre de tentatives ;
- très forte concentration temporelle ;
- nombreux ports destination ;
- nombreuses conversations très courtes ;
- même source et même cible.

Une activité inhabituelle n’est toutefois pas automatiquement malveillante. Un administrateur, un scanner de vulnérabilités ou un outil d’inventaire peut produire un comportement similaire.

---

## 10. Triage initial

### 10.1 Hiérarchie des protocoles

![Hiérarchie des protocoles](../captures/08-hierarchie-protocoles-investigation.png)

La capture contient **5 042 trames**. Le trafic observé est majoritairement IPv4 et TCP, avec également du trafic UDP, QUIC, TLS, DNS, LDAP, ARP et HTTP.

Cette vue montre que l’activité investiguée se trouve au milieu d’un trafic de fond plus large.

### 10.2 Conversations IPv4

![Conversations IPv4](../captures/09-conversations-ipv4-investigation.png)

La conversation entre `192.168.230.219` et `192.168.230.220` comprend environ **2 042 paquets**, dont environ **2 028 dans le sens source → cible** contre **14 dans le sens inverse**.

La communication est donc fortement asymétrique et concentrée sur environ **4,63 secondes**.

---

## 11. Filtres Wireshark utilisés

### Activité impliquant la source

```text
ip.addr == 192.168.230.219
```

### Communication entre les deux hôtes

```text
ip.addr == 192.168.230.219 && ip.addr == 192.168.230.220
```

### Trafic initié par la source vers la cible

```text
ip.src == 192.168.230.219 && ip.dst == 192.168.230.220
```

### SYN initiaux

```text
ip.src == 192.168.230.219 && ip.dst == 192.168.230.220 && tcp.flags.syn == 1 && tcp.flags.ack == 0
```

### Réponses SYN/ACK

```text
ip.src == 192.168.230.220 && ip.dst == 192.168.230.219 && tcp.flags.syn == 1 && tcp.flags.ack == 1
```

### RST provenant de la cible

```text
ip.src == 192.168.230.220 && ip.dst == 192.168.230.219 && tcp.flags.reset == 1
```

Ces filtres permettent de passer d’une capture globale à une analyse ciblée du comportement entre les deux systèmes.

---

## 12. Analyse des tentatives TCP SYN

![Multiples SYN](../captures/10-syn-multiples-ports.png)

Le filtre sur les SYN initiaux affiche environ **2 000 paquets**, soit près de 40 % des 5 042 paquets capturés.

Les ports destination varient fortement, avec notamment des tentatives vers `53`, `23`, `443`, `587`, `3306`, `445`, `111`, `5900`, `995`, `1723` et de nombreux autres ports.

L’élément déterminant n’est pas un port isolé mais le pattern :

```text
même source
+ même cible
+ très nombreux ports
+ très courte période
```

Ce comportement est fortement compatible avec une activité automatisée de découverte de services.

---

## 13. Analyse des réponses SYN/ACK

![Services accessibles](../captures/11-synack-ports-accessibles.png)

Quatorze réponses SYN/ACK ont été observées.

Parmi les ports visibles figurent notamment :

| Port | Service généralement associé |
|---:|---|
| 53 | DNS |
| 135 | RPC Endpoint Mapper |
| 139 | NetBIOS Session Service |
| 389 | LDAP |
| 445 | SMB / Microsoft-DS |
| 464 | Kerberos password change |
| 636 | LDAPS |
| 3268 | Global Catalog |
| 3269 | Global Catalog SSL |
| 3389 | RDP |
| 5985 | WinRM |

Ces réponses indiquent que plusieurs services TCP étaient accessibles depuis la source au moment de la capture.

Un port accessible n’est pas, à lui seul, une vulnérabilité et ne démontre aucune compromission.

---

## 14. Analyse des conversations TCP

![Conversations TCP](../captures/12-conversations-tcp-multiples-ports.png)

La vue Conversations TCP affiche environ **1 988 conversations filtrées**.

De nombreuses conversations ne comportent qu’un très petit nombre de paquets et ciblent des ports différents de la même machine.

Ce profil diffère d’une utilisation applicative classique, dans laquelle une connexion établie transporte généralement plusieurs échanges utiles. Ici, le pattern est davantage compatible avec le test rapide de nombreux services.

---

## 15. Corrélation détaillée — Port 445/TCP

Filtre utilisé :

```text
ip.addr == 192.168.230.219 && ip.addr == 192.168.230.220 && tcp.port == 445
```

![Corrélation TCP port 445](../captures/13-correlation-tcp-port-445.png)

La séquence observée est :

1. `192.168.230.219 → 192.168.230.220:445` — SYN ;
2. `192.168.230.220:445 → 192.168.230.219` — SYN/ACK ;
3. `192.168.230.219 → 192.168.230.220:445` — ACK ;
4. `192.168.230.219 → 192.168.230.220:445` — RST/ACK.

Cette séquence montre qu’une connexion TCP a été établie puis rapidement interrompue par la source.

L’observation est cohérente avec un test d’accessibilité du service plutôt qu’avec une session SMB prolongée.

---

## 16. Chronologie de l’incident

### 16.1 Bornes exactes de l’activité SYN

**Premier SYN observé**

```text
Paquet : 2036
Timestamp : 2026-08-26 00:24:36,884959600
Source : 192.168.230.219
Destination : 192.168.230.220
Port destination : 22/TCP
```

**Dernier SYN observé**

```text
Paquet : 4079
Timestamp : 2026-08-26 00:24:41,513415900
Source : 192.168.230.219
Destination : 192.168.230.220
Port destination : 2008/TCP
```

La durée exacte entre ces deux événements est :

**4,6284563 secondes**, soit environ **4,63 secondes**.

Avec environ 2 000 SYN observés, cela représente environ **432 tentatives SYN par seconde en moyenne**.

### 16.2 Corrélation temporelle des services

![Chronologie réseau](../captures/14-chronologie-services-reseau.png)

Plusieurs services importants sont testés dans une fenêtre temporelle très courte, notamment SMB, RDP, LDAP, LDAPS et WinRM.

La répétition de séquences TCP similaires sur plusieurs ports renforce l’hypothèse d’une activité automatisée.

---

## 17. Indicateurs réseau et IOC

Il est important de ne pas qualifier abusivement chaque observation d’IOC.

### Éléments observés

| Élément | Qualification |
|---|---|
| `192.168.230.219` | Hôte source à investiguer |
| `192.168.230.220` | Système ciblé |
| ≈ 2 000 SYN | Indicateur comportemental suspect |
| Nombreux ports ciblés | Indicateur comportemental |
| ≈ 1 988 conversations TCP | Indicateur comportemental |
| 14 SYN/ACK | Services accessibles |
| 4,63 secondes | Forte concentration temporelle |
| ≈ 432 SYN/s | Intensité anormale à contextualiser |

Les deux adresses sont privées et propres au laboratoire. Elles ne constituent donc pas, isolément, des IOC de compromission.

### Conclusion IOC

**IOC de compromission confirmé : aucun.**

**Indicateurs comportementaux de reconnaissance : plusieurs.**

---

## 18. Faits, observations et hypothèses

### Faits démontrés par le PCAP

- `192.168.230.219` communique avec `192.168.230.220` ;
- environ 2 000 SYN sont envoyés ;
- de nombreux ports sont ciblés ;
- l’activité est concentrée sur 4,63 secondes ;
- plusieurs services répondent par SYN/ACK.

### Observations analytiques

- l’activité est fortement automatisée ;
- les connexions sont nombreuses et très courtes ;
- plusieurs services semblent être testés plutôt qu’utilisés.

### Hypothèse

**Activité automatisée de reconnaissance / découverte de services TCP fortement probable.**

### Éléments non démontrés

Le PCAP ne démontre pas :

- l’intention malveillante de l’opérateur ;
- l’exploitation d’un service ;
- une authentification réussie ;
- l’exécution de code ;
- une compromission du serveur ;
- la présence d’un malware ;
- l’identité certaine de l’outil ayant généré le trafic.

---

## 19. Mapping MITRE ATT&CK

### Tactique

**Discovery — TA0007**

### Technique

**Network Service Scanning — T1046**

### Justification

Le mapping est fondé sur la combinaison des observations suivantes :

- environ 2 000 tentatives TCP ;
- grand nombre de ports ciblés ;
- même source et même cible ;
- activité concentrée sur quelques secondes ;
- identification de plusieurs services accessibles ;
- connexions généralement très courtes.

### Niveau de confiance

**Élevé** pour la correspondance comportementale avec `T1046`.

Cette association ne permet toutefois pas de déterminer si l’activité était autorisée ou hostile.

Aucun autre mapping MITRE ATT&CK n’est retenu faute de preuves suffisantes d’exploitation, d’accès initial, de mouvement latéral ou d’autres actions adverses.

---

## 20. Impact potentiel

L’activité de reconnaissance n’a pas démontré de compromission, mais elle permet de mieux connaître la surface réseau de la cible.

Les services découverts pourraient fournir à un attaquant des informations utiles pour préparer des actions ultérieures ciblant, par exemple :

- l’administration distante ;
- les services d’annuaire ;
- le partage de fichiers ;
- l’authentification ;
- d’éventuels services vulnérables.

Il s’agit d’un **impact potentiel**, et non d’un impact effectivement observé dans le PCAP.

---

## 21. Sévérité et priorité

### Sévérité proposée

**Moyenne**

### Justification

Facteurs augmentant la sévérité :

- activité automatisée ;
- volume important de tentatives ;
- nombreux ports ciblés ;
- présence de services d’administration et Active Directory accessibles.

Facteurs limitant la sévérité :

- aucune exploitation démontrée ;
- aucune authentification malveillante démontrée ;
- aucune compromission confirmée ;
- aucune interruption de service observée.

### Priorité opérationnelle

La priorité dépendrait du contexte de la source.

Un scanner de sécurité autorisé pourrait rendre l’activité légitime. À l’inverse, un poste utilisateur sans raison opérationnelle de scanner un serveur justifierait une investigation et une escalade plus rapides.

---

## 22. Faux positifs et explications légitimes possibles

Un comportement de scanning peut notamment provenir :

- d’un administrateur réseau ;
- d’un scanner de vulnérabilités ;
- d’un outil d’inventaire ;
- d’un contrôle de conformité ;
- d’un outil de supervision ;
- d’un exercice de cybersécurité autorisé.

La question essentielle pour le SOC serait donc :

> La source `192.168.230.219` était-elle autorisée à effectuer cette activité ?

---

## 23. Investigations complémentaires

Dans un environnement de production, l’analyse devrait être poursuivie avec d’autres sources de télémétrie.

### Sur la source `192.168.230.219`

- utilisateur connecté ;
- processus responsable des connexions ;
- ligne de commande ;
- journaux système ;
- données EDR ;
- événements Sysmon.

### Sur la cible `192.168.230.220`

- connexions acceptées ;
- journaux Windows ;
- authentifications ;
- événements SMB, RDP, LDAP et WinRM ;
- logs du pare-feu local ;
- données EDR.

### Infrastructure

- pare-feu réseau ;
- IDS/IPS ;
- SIEM ;
- DNS ;
- DHCP.

L’objectif serait de déterminer si l’activité s’est limitée à la reconnaissance ou si des actions supplémentaires ont suivi.

---

## 24. Recommandations Blue Team

En cas d’activité similaire non autorisée :

1. vérifier si la source appartient à un scanner ou à un administrateur autorisé ;
2. identifier l’utilisateur et le processus à l’origine des connexions ;
3. rechercher si la même source a ciblé d’autres systèmes ;
4. examiner les journaux Windows et les données EDR des deux hôtes ;
5. rechercher des authentifications ou connexions ultérieures sur les services découverts ;
6. corréler avec les journaux du pare-feu, IDS/IPS et SIEM ;
7. limiter l’accès à RDP, SMB, WinRM et aux services d’administration aux systèmes nécessaires ;
8. appliquer la segmentation réseau et le moindre privilège réseau ;
9. envisager une règle de détection pour les scans de ports rapides ou anormaux ;
10. escalader l’incident si une activité postérieure à la reconnaissance est identifiée.

---

## 25. Verdict final de l’analyste

**Type d’activité :** reconnaissance réseau / découverte automatisée de services TCP fortement probable.

**Tactique MITRE ATT&CK :** `TA0007 — Discovery`

**Technique MITRE ATT&CK :** `T1046 — Network Service Scanning`

**Niveau de confiance :** élevé.

**Sévérité proposée :** moyenne.

**Compromission confirmée :** non.

**Exploitation confirmée :** non.

L’analyse du trafic révèle environ 2 000 tentatives TCP SYN provenant de `192.168.230.219` et ciblant de nombreux ports de `192.168.230.220` en environ 4,63 secondes. Plusieurs services ont répondu positivement, et la répétition de connexions très courtes est fortement compatible avec une activité automatisée de découverte de services.

La capture réseau ne démontre cependant ni exploitation ni compromission. Dans un SOC réel, la décision d’escalade dépendrait notamment de l’autorisation de la source et de la présence éventuelle d’activités postérieures à la reconnaissance.

---

## 26. Compétences démontrées

- Analyse de paquets avec Wireshark
- Ethernet II, IPv4, ICMP et TCP
- Filtres d’affichage
- Analyse des flags TCP
- Triage de PCAP
- Analyse de trafic suspect
- Corrélation d’événements réseau
- Construction d’une chronologie
- Qualification d’indicateurs réseau
- Distinction entre IOC et indicateur comportemental
- Analyse Blue Team
- MITRE ATT&CK
- Évaluation de sévérité
- Identification des limites d’une investigation
- Recommandations SOC
- Documentation technique professionnelle

---

## 27. Conclusion générale

Le Projet 01 a commencé par l’analyse fondamentale d’un échange ICMP et a évolué vers une investigation réseau orientée SOC.

Cette progression démontre l’utilisation de Wireshark à deux niveaux : comprendre précisément le fonctionnement des protocoles réseau et exploiter les paquets comme source de preuves lors d’une investigation défensive.

Le projet montre également l’importance de ne pas confondre anomalie, comportement suspect et compromission confirmée. Une conclusion SOC doit rester proportionnée aux preuves disponibles et indiquer clairement les investigations complémentaires nécessaires.
