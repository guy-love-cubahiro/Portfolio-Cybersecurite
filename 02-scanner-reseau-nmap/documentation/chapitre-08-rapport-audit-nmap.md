# Rapport d'audit réseau avec Nmap

## Projet 02 – Scanner réseau avec Nmap

**Auteur :** Guy Love Cubahiro

**Date :** 05 aout 2026

**Version :** 1.0


# Table des matières

1. Contexte
2. Objectifs
3. Environnement du laboratoire
4. Méthodologie
5. Outils utilisés
6. Résultats
7. Analyse
8. Limites de l'audit
9. Recommandations
10. Conclusion
11. Compétences developpées


# 1. Contexte

Dans le cadre de la création de mon portfolio de cybersécurité, j'ai réalisé un audit réseau dans un environnement de laboratoire à l'aide de l'outil Nmap.

Ce laboratoire avait pour objectif de reproduire la démarche utilisée par un analyste cybersécurité lors d'une phase de reconnaissance autorisée.

L'objectif n'était pas de compromettre les systèmes, mais d'identifier les services accessibles, d'analyser leur configuration et de comprendre les limites ainsi que les bonnes pratiques liées à l'utilisation de Nmap.

# 2. Objectifs

Les objectifs de ce laboratoire étaient les suivants :

- Vérifier la disponibilité des hôtes.
- Identifier les ports TCP et UDP accessibles.
- Déterminer les services exécutés sur les ports ouverts.
- Identifier les versions des services.
- Estimer le système d'exploitation.
- Utiliser les scripts NSE adaptés aux services détectés.
- Vérifier certaines vulnérabilités connues.
- Interpréter les résultats de manière professionnelle.
- Produire un rapport technique structuré.

# 3. Environnement du laboratoire

## Machine hôte

- Windows 11
- VMware Workstation Pro 17
- 32 Go de RAM
- Environ 200 Go d'espace libre

## Machines virtuelles

- Windows Server 2022
- Windows 11
- Kali Linux

## Réseau

- Réseau NAT VMware
- Communications testées entre les trois machines virtuelles

## Outils utilisés

- Nmap
- Wireshark
- Windows PowerShell
- GitHub Desktop

# 4. Méthodologie

L'audit a été réalisé de manière progressive afin de limiter le trafic réseau et d'obtenir des informations fiables avant d'effectuer des analyses plus approfondies.

Les différentes étapes ont été les suivantes :

1. Vérification de la disponibilité de la cible.
2. Identification des ports TCP ouverts.
3. Analyse des ports UDP.
4. Détection des services et de leurs versions.
5. Estimation du système d'exploitation.
6. Utilisation des scripts NSE adaptés aux services détectés.
7. Vérification de certaines vulnérabilités connues.
8. Analyse et interprétation des résultats.
9. Rédaction du rapport d'audit.

## Pourquoi cette méthodologie ?

Cette approche permet de progresser du moins intrusif vers le plus intrusif.

Chaque étape fournit des informations qui permettent de sélectionner les analyses suivantes de manière pertinente, tout en limitant le trafic réseau et les risques de perturber les services analysés.

Cette méthode correspond aux bonnes pratiques utilisées lors des audits de sécurité et des activités de reconnaissance autorisées.

# 5. Outils utilisés

| Outil | Utilisation |
|--------|-------------|
| Nmap | Découverte des hôtes, analyse des ports, identification des services et utilisation des scripts NSE |
| Wireshark | Capture et analyse du trafic réseau |
| VMware Workstation Pro | Virtualisation de l'environnement de laboratoire |
| Windows PowerShell | Vérification locale des services et de la configuration |
| GitHub Desktop | Gestion des versions du projet |
| GitHub | Publication et documentation du laboratoire |

# 6. Résultats

## Découverte des hôtes

Les analyses ont confirmé que les machines virtuelles du laboratoire étaient joignables sur le réseau et pouvaient communiquer entre elles après la configuration des règles de pare-feu nécessaires.

## Analyse des ports

Les analyses TCP ont permis d'identifier plusieurs services accessibles sur le serveur Windows.

Les principaux services observés étaient :

- Port 3389/TCP : Remote Desktop Protocol (RDP)
- Port 5985/TCP : Windows Remote Management (WinRM)

Le port 445/TCP (SMB) est apparu à l'état **filtered**, indiquant qu'un pare-feu ou un mécanisme de sécurité empêchait les communications avec ce service.

## Détection des services

L'utilisation de l'option `-sV` a permis d'identifier les services exécutés sur les ports ouverts et d'obtenir des informations complémentaires sur leur version.

## Estimation du système d'exploitation

Les analyses réalisées avec Nmap ont permis d'obtenir une estimation du système d'exploitation de la machine analysée, qui était cohérente avec l'environnement de laboratoire utilisé.

## Utilisation des scripts NSE

Plusieurs scripts NSE ont été exécutés afin d'obtenir des informations complémentaires sur les services détectés.

Les principaux groupes de scripts utilisés étaient :

- HTTP
- RDP
- SMB

Les résultats ont montré que les scripts fonctionnent uniquement lorsque le service ciblé est accessible.

Les scripts SMB n'ont pas pu fournir d'informations supplémentaires car le port 445 était filtré.

# 7. Analyse

Les résultats obtenus montrent qu'une analyse efficace ne dépend pas uniquement des outils utilisés, mais également de l'accessibilité des services.

Les services RDP et WinRM étant accessibles, ils ont pu être analysés avec les scripts NSE appropriés.

À l'inverse, le service SMB n'a pas pu être étudié en profondeur en raison du filtrage du port 445.

Cette situation illustre l'importance de toujours interpréter les résultats dans leur contexte et de ne pas conclure qu'un service est sécurisé uniquement parce qu'aucune vulnérabilité n'a été détectée.

L'absence de résultat ne constitue pas une preuve d'absence de vulnérabilité.

# 8. Limites de l'audit

Cet audit a été réalisé dans un environnement de laboratoire contrôlé.

Les résultats obtenus dépendent de la configuration des machines, des règles de pare-feu et des services accessibles au moment de l'analyse.

Certains services, comme SMB, n'ont pas pu être analysés en profondeur car le port 445 était filtré.

L'absence de vulnérabilités détectées ne permet pas d'affirmer qu'un système est totalement sécurisé. Une évaluation complète nécessiterait l'utilisation d'autres outils spécialisés, une analyse des correctifs de sécurité, une revue des configurations et, si le contexte l'autorise, des tests complémentaires.

# 9. Recommandations

À la suite de cet audit, les recommandations suivantes peuvent être formulées :

- Maintenir les systèmes d'exploitation et les applications à jour.
- Limiter l'exposition des services réseau aux seuls besoins opérationnels.
- Restreindre l'accès aux services sensibles (RDP, WinRM, SMB) aux utilisateurs et réseaux autorisés.
- Maintenir des règles de pare-feu adaptées afin de limiter les communications non nécessaires.
- Réaliser des audits de sécurité réguliers avec plusieurs outils complémentaires.
- Vérifier régulièrement les journaux d'événements afin de détecter les activités inhabituelles.

# 10. Conclusion

Ce projet m'a permis d'acquérir une compréhension approfondie de l'utilisation de Nmap dans le cadre d'un audit réseau.

Au-delà de la maîtrise des commandes, j'ai appris à adopter une méthodologie structurée, à sélectionner les analyses adaptées au contexte, à interpréter les résultats de manière critique et à reconnaître les limites des outils utilisés.

Ce laboratoire constitue une étape importante dans le développement de mes compétences en cybersécurité et dans la construction de mon portfolio professionnel en vue d'un poste d'analyste SOC junior.

# 11. Compétences développées

Au cours de ce projet, j'ai développé les compétences suivantes :

- Réalisation d'un audit réseau avec Nmap.
- Identification des ports TCP et UDP.
- Détection des services et de leurs versions.
- Estimation du système d'exploitation.
- Utilisation des scripts NSE (HTTP, RDP et SMB).
- Analyse des résultats et interprétation des états des ports.
- Compréhension des limites des outils de scan.
- Rédaction d'un rapport d'audit professionnel.
- Documentation d'un projet technique sur GitHub.
