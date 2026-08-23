# Chapitre 2 — Découverte des hôtes avec Nmap

## Objectif

Identifier les hôtes actifs présents sur le réseau du laboratoire avant de réaliser des analyses plus approfondies.

## Commande utilisée

```bash
nmap -sn 192.168.230.0/24
```

## Résultats

| Adresse IP | Hôte identifié | Observation |
|------------|----------------|-------------|
| 192.168.230.1 | Infrastructure VMware | Hôte actif |
| 192.168.230.2 | Passerelle VMware | Hôte actif |
| 192.168.230.218 | Windows 11 | Machine cible |
| 192.168.230.219 | Kali Linux | Machine d'analyse |
| 192.168.230.220 | Windows Server 2022 | Machine cible |
| 192.168.230.254 | Infrastructure VMware | Hôte actif |

## Analyse

Le scan de découverte a identifié six hôtes actifs sur le réseau virtuel.

Les trois machines du laboratoire ont été détectées avec succès. Deux équipements de l'infrastructure réseau VMware ainsi que la passerelle virtuelle ont également répondu aux requêtes de découverte.

Cette étape confirme que le laboratoire est correctement configuré avant les analyses de ports.

## Premier scan de ports

Commande utilisée :

```bash
nmap 192.168.230.218
```

### Résultat

Le scan n'a identifié aucun port TCP ouvert parmi les 1000 ports analysés.

Tous les ports apparaissent dans l'état **filtered**, ce qui indique que les paquets envoyés par Nmap n'ont reçu aucune réponse.

### Analyse

Ce comportement est généralement observé lorsqu'un pare-feu bloque les connexions TCP entrantes. Bien que la machine soit active sur le réseau, les règles de sécurité empêchent Nmap de déterminer l'état des ports analysés.

## Analyse du premier scan de ports

Une analyse ciblée a été réalisée sur Windows Server 2022.

### Commande utilisée

```bash
nmap 192.168.230.220
```

### Résultats

| Port | État | Service |
|------|------|---------|
| 3389/TCP | Open | Microsoft Remote Desktop (RDP) |
| 5985/TCP | Open | Windows Remote Management (WinRM) |

### Analyse

Le serveur expose deux services réseau.

Le port 3389 confirme que le Bureau à distance est activé et accessible.

Le port 5985 indique que le service Windows Remote Management (WinRM) est disponible pour l'administration distante.

Les autres ports analysés apparaissent comme **filtered**, ce qui suggère qu'ils sont protégés par le pare-feu Windows ou par des règles de filtrage réseau.

## Détection des services et des versions (-sV)

### Objectif

Identifier les services exécutés sur les ports ouverts et tenter d'en déterminer la version afin d'obtenir des informations supplémentaires pour l'analyse de sécurité.

### Commande utilisée

```bash
nmap -sV 192.168.230.220
```

### Résultats obtenus

| Port | État | Service | Informations détectées |
|------:|:----:|----------|------------------------|
| 3389 | Open | ms-wbt-server | Microsoft Terminal Services |
| 5985 | Open | HTTP | Microsoft HTTPAPI httpd 2.0 |

### Analyse

Le scan `-sV` a permis d'obtenir des informations plus précises sur les services exécutés par le serveur Windows. Contrairement au scan de ports classique, cette commande tente d'identifier les logiciels qui écoutent sur les ports ouverts en échangeant des messages avec eux. Ces informations sont essentielles pour évaluer la sécurité des services et rechercher d'éventuelles vulnérabilités connues.

## Détection du système d'exploitation (-O)

### Objectif

Identifier le système d'exploitation probable de la machine cible à partir de son empreinte réseau (TCP/IP Fingerprinting).

### Commande utilisée

```bash
sudo nmap -O 192.168.230.220
```

### Résultats obtenus

- Système détecté : Microsoft Windows (estimation)
- Niveau de confiance : 96 %
- Device type : General purpose
- Distance réseau : 1 hop

### Analyse

Le scan a identifié un système Microsoft Windows avec un niveau de confiance élevé. Toutefois, Nmap indique que les conditions du test ne sont pas idéales, car il n'a pas trouvé de port fermé. Le pare-feu de la machine limite donc la précision de la détection. Les résultats doivent être interprétés comme une forte probabilité et non comme une certitude.

# Résumé des commandes étudiées

| Commande | Objectif |
|----------|----------|
| `nmap -sn 192.168.230.0/24` | Découvrir les hôtes actifs sur le réseau |
| `nmap 192.168.230.220` | Identifier les ports TCP ouverts |
| `nmap -sV 192.168.230.220` | Identifier les services et tenter de déterminer leur version |
| `sudo nmap -O 192.168.230.220` | Estimer le système d'exploitation de la machine cible |

# Ce que j'ai appris

Au cours de ce chapitre, j'ai appris à :

- découvrir les machines actives d'un réseau local ;
- interpréter les états des ports (open, closed, filtered) ;
- identifier les services exécutés sur les ports ouverts ;
- comprendre le fonctionnement de la détection des versions avec `-sV` ;
- utiliser la détection du système d'exploitation avec `-O` ;
- interpréter les résultats de Nmap avec un esprit critique ;
- distinguer les observations des conclusions.

# Bonnes pratiques retenues

- Toujours commencer par identifier les hôtes actifs.
- Ne jamais supposer qu'un service est vulnérable uniquement parce qu'un port est ouvert.
- Interpréter les résultats de Nmap en tenant compte des limites de l'outil.
- Confirmer les hypothèses à l'aide de plusieurs sources d'information.
- Réaliser les scans uniquement dans un environnement autorisé.
