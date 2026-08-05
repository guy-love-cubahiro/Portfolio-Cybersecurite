# Scan d'un port spécifique

## Commande utilisée

```bash
nmap -p 3389 192.168.230.220
```

## Objectif

Analyser uniquement le port TCP 3389 afin de vérifier si le service Bureau à distance (RDP) est disponible.

## Résultat attendu

Si le service RDP est actif et autorisé par le pare-feu, Nmap doit afficher :

3389/tcp open ms-wbt-server
```

# Scan de plusieurs ports spécifiques

## Commande utilisée

```bash
nmap -p 22,80,443,3389,5985 192.168.230.220
```

## Objectif

Analyser plusieurs services précis en une seule commande sans scanner les 1 000 ports TCP par défaut.

## Ports sélectionnés

| Port | Service généralement associé |
|------:|------------------------------|
| 22 | SSH |
| 80 | HTTP |
| 443 | HTTPS |
| 3389 | RDP |
| 5985 | WinRM |

## Hypothèse

Les ports 3389 et 5985 devraient apparaître comme ouverts, car les services RDP et WinRM ont déjà été détectés sur le serveur.

# Scan d'une plage de ports

## Commande utilisée

```bash
nmap -p 3380-3390 192.168.230.220
```

## Objectif

Analyser une plage de ports autour du port RDP afin de vérifier si un service écoute sur un port voisin.

## Avantages

- Plus rapide qu'un scan des 1000 ports.
- Plus complet qu'un scan d'un seul port.
- Permet de détecter un service déplacé vers un port proche.
```

# Scan complet des ports TCP

## Commande utilisée

```bash
nmap -p- 192.168.230.220
```

## Objectif

Analyser les 65 535 ports TCP afin de détecter les services qui pourraient être installés sur des ports non standards.

## Avantages

- Détecte les services installés sur n'importe quel port TCP.
- Très utile lors d'un audit complet ou d'un test d'intrusion.

## Inconvénients

- Plus long qu'un scan classique.
- Génère davantage de trafic réseau.
- Peut être plus facilement détecté par les systèmes de sécurité.
```

# Conclusion du scan complet

Le scan des 65 535 ports TCP confirme que seuls les ports 3389 (Remote Desktop Protocol) et 5985 (Windows Remote Management) sont accessibles sur le serveur Windows Server 2022.

Les 65 533 autres ports apparaissent dans l'état **filtered**, ce qui indique que le pare-feu ou une politique de filtrage empêche Nmap de déterminer leur état exact.

Ce type de scan est particulièrement utile lors d'un audit complet afin de détecter des services exécutés sur des ports non standards.

## Premier scan UDP

## Commande utilisée

```bash
sudo nmap -sU 192.168.230.220
```

## Objectif

Découvrir les services utilisant le protocole UDP et observer les différences d'interprétation par rapport aux scans TCP.

## Hypothèse

Le scan UDP devrait être plus lent que les scans TCP et certains ports pourraient apparaître dans l'état `open|filtered`, car l'absence de réponse ne permet pas toujours à Nmap de distinguer un port ouvert d'un port filtré.
```

## Résultat observé

Le scan UDP des 1000 ports n'a identifié aucun port comme ouvert ou fermé avec certitude.

Tous les ports apparaissent dans l'état `open|filtered`, ce qui signifie que Nmap n'a reçu aucune réponse lui permettant de distinguer un port ouvert d'un port filtré.

Contrairement à TCP, UDP n'établit pas de connexion avant l'envoi des données. L'absence de réponse est donc normale dans de nombreux cas et complique l'interprétation des résultats.

# Vérification des ports UDP sous Windows

## Commande utilisée

```powershell
Get-NetUDPEndpoint
```

## Objectif

Comparer les résultats du scan UDP réalisé avec Nmap aux ports UDP réellement en écoute sur le système.

## Résultat

Le système montre notamment des services en écoute sur les ports UDP 123 (NTP) et 3389 (RDP). Les ports 53, 67, 68 et 161 n'apparaissent pas dans la liste des endpoints UDP.

## Conclusion

La comparaison montre qu'un scan Nmap ne suffit pas toujours pour confirmer l'état d'un service UDP. Il est recommandé de recouper les résultats avec des informations provenant directement du système d'exploitation.

# Comparaison des niveaux de timing

## Commandes utilisées

```bash
nmap -T3 192.168.230.220
nmap -T4 192.168.230.220
```

## Résultats

Les deux commandes ont identifié les mêmes ports ouverts :

- 3389/tcp (RDP)
- 5985/tcp (WinRM)

Le scan avec `-T4` s'est terminé légèrement plus rapidement que celui avec `-T3`.

## Conclusion

Dans un laboratoire VMware à faible latence, la différence de temps entre `-T3` et `-T4` est faible. Le choix d'un niveau de timing doit être adapté au contexte du réseau et à l'objectif du scan.
