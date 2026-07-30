# Chapitre 1 — Préparation de l'environnement

## Objectif

Préparer le laboratoire avant de réaliser les premiers scans réseau avec Nmap.

## Environnement utilisé

| Élément | Description |
|----------|-------------|
| Système d'analyse | Kali Linux |
| Machines cibles | Windows 11, Windows Server 2022 |
| Hyperviseur | VMware Workstation Pro 17 |
| Outil principal | Nmap 7.99 |

## Vérification de l'adresse IP

La machine Kali Linux possède une adresse IPv4 sur le réseau virtuel.

Cette adresse permettra d'effectuer les analyses réseau dans les chapitres suivants.

## Vérification de Nmap

La commande `nmap --version` confirme que Nmap est correctement installé et prêt à être utilisé.

## Conclusion

L'environnement de laboratoire est correctement préparé. Les prochaines étapes consisteront à découvrir les hôtes présents sur le réseau.

## Vérification de la connectivité réseau

Avant d'utiliser Nmap, la connectivité entre les machines du laboratoire a été vérifiée à l'aide de la commande `ping`.

Les résultats montrent que :

- Kali Linux communique correctement avec Windows 11.
- Kali Linux communique correctement avec Windows Server 2022.
- Aucun paquet n'a été perdu lors des tests.
- Les temps de réponse sont inférieurs à 2 ms, ce qui est attendu dans un laboratoire virtuel VMware.

Cette vérification confirme que les trois machines appartiennent au même réseau virtuel et sont prêtes pour les analyses réseau du chapitre suivant.