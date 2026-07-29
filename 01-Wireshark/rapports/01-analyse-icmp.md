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

---

## 8. Observations

Aucune perte de paquet n'a été observée.

Les temps de réponse étaient faibles, ce qui indique une bonne connectivité réseau dans le laboratoire.

Le filtre ICMP a permis d'isoler rapidement les paquets liés au test.

---

## 9. Compétences développées

- Utilisation de Wireshark
- Capture de trafic réseau
- Utilisation des filtres d'affichage
- Analyse des paquets ICMP
- Validation de la connectivité réseau