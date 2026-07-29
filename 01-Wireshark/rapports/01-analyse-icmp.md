# Laboratoire 01 – Analyse ICMP avec Wireshark

## 1. Objectif

L'objectif de ce laboratoire est de capturer et d'analyser des paquets ICMP générés par une commande `ping` à l'aide de Wireshark.

---

## 2. Environnement

### Machine hôte
- Windows 11
- Wireshark
- VMware Workstation Pro

### Machines virtuelles
- Windows 11
- Windows Server 2022
- Kali Linux

---

## 3. Topologie du laboratoire

Le trafic a été capturé depuis la machine hôte sur l'interface réseau virtuelle **VMnet8**.

Le test consistait à envoyer une requête ICMP (Ping) depuis la machine virtuelle Windows 11 vers Windows Server 2022.

---

## 4. Outils utilisés

- Wireshark
- VMware Workstation Pro
- Windows 11
- Windows Server 2022

---

## 5. Commande utilisée

```cmd
ping 192.168.230.220
```

---

## 6. Résultats

La capture réseau a été réalisée avec succès sur l'interface VMware VMnet8.

Le filtre suivant a été appliqué dans Wireshark :

```text
icmp
```

La capture a permis d'observer les échanges ICMP entre la machine Windows 11 et Windows Server 2022.

---

## 7. Analyse des paquets

L'analyse de la capture montre que :

- Windows 11 envoie une requête **ICMP Echo Request**.
- Windows Server 2022 répond par une **ICMP Echo Reply**.
- Chaque requête reçoit une réponse.
- La communication confirme que les deux machines peuvent communiquer sur le réseau.

Les adresses IP observées sont :

- Source : 192.168.230.218
- Destination : 192.168.230.220

Analyse :

L'analyse a été réalisée à partir d'une capture ICMP obtenue avec Wireshark lors d'un échange de ping entre une machine Windows 11 (192.168.230.218) et un serveur Windows Server 2022 (192.168.230.220) sur un réseau virtuel VMware.

L'analyse de l'en-tête Ethernet II a permis d'identifier les adresses MAC source et destination utilisées pour la communication au niveau de la couche Liaison de données.

L'analyse de l'en-tête IPv4 a permis d'interpréter plusieurs champs importants :
- Version : IPv4
- Longueur de l'en-tête : 20 octets
- DSCP : CS0 (trafic normal)
- TTL : 128
- Protocole : ICMP
- Checksum IPv4 : présent, mais non vérifié par Wireshark en raison du checksum offloading
- Adresse IP source : 192.168.230.218
- Adresse IP destination : 192.168.230.220

L'analyse ICMP montre qu'une requête Echo Request (Type 8) est envoyée vers le serveur, suivie d'une Echo Reply (Type 0), confirmant que l'hôte distant est joignable.

Les champs Identifier et Sequence Number sont identiques dans la requête et la réponse, ce qui permet d'associer correctement les deux paquets.

Le temps de réponse observé est de 0,336 ms, indiquant une communication très rapide au sein du réseau virtuel.

## 8. Observations

Aucune perte de paquet n'a été observée.

Les temps de réponse étaient faibles, ce qui indique une bonne connectivité réseau dans le laboratoire.

Le filtre ICMP a permis d'isoler rapidement les paquets liés au test.

Point de vue d'un analyste SOC

Cette analyse confirme que la communication ICMP entre les deux hôtes s'est déroulée normalement. Les en-têtes Ethernet, IPv4 et ICMP sont cohérents, les checksums sont valides lorsqu'ils sont vérifiés, et la réponse est reçue sans perte avec un temps de réponse très faible.

Lors d'une investigation, ce type d'analyse permet de confirmer la connectivité entre deux machines, de vérifier l'intégrité des paquets et d'identifier rapidement l'origine et la destination des communications.

---

## 9. Compétences développées

- Utilisation de Wireshark
- Capture de trafic réseau
- Utilisation des filtres d'affichage
- Analyse des paquets ICMP
- Validation de la connectivité réseau

---

## 10. Preuves du laboratoire

Les captures d'écran et le fichier de capture réseau sont disponibles dans les dossiers suivants :

- `captures/`
- `captures-pcap/`

---

## 11. Fichiers associés

| Type | Nom |
|------|-----|
| Capture réseau | `01-ping-windows11-vers-server.pcapng` |
| Capture d'écran | `02-capture-icmp.png` |

---

## 12. Ce que j'ai appris

À l'issue de ce laboratoire, je suis capable de :

- Capturer du trafic réseau avec Wireshark.
- Utiliser les interfaces réseau VMware (VMnet8).
- Filtrer les paquets ICMP.
- Identifier une requête Echo Request et une réponse Echo Reply.
- Valider la connectivité entre deux machines virtuelles.
- Sauvegarder une capture réseau au format `.pcapng`.

---

## 13. Compétences démontrées

- Analyse réseau
- Diagnostic de connectivité
- Wireshark
- VMware Workstation
- Documentation technique
- Git
- GitHub

---

## 14. Conclusion

Ce premier laboratoire m'a permis de découvrir le fonctionnement de Wireshark et d'observer le protocole ICMP dans un environnement virtualisé.

Cette analyse constitue la base des prochains laboratoires.