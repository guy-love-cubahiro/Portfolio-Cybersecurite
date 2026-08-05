# Utilisation des scripts par défaut (-sC)

## Commande utilisée

```bash
nmap -sC 192.168.230.220
```

## Objectif

Exécuter les scripts NSE par défaut afin de collecter des informations complémentaires sur les services détectés.

## Informations obtenues

- Certificat SSL du service RDP
- Nom NetBIOS de la machine
- Nom DNS de la machine
- Version du système d'exploitation
- Informations RDP
- Décalage horaire entre le serveur et la machine de scan

## Conclusion

Les scripts NSE permettent d'obtenir des informations détaillées sur les services sans avoir besoin d'exploiter le système ou de s'y authentifier. Ils constituent une étape essentielle de la phase de reconnaissance lors d'un audit de sécurité.

# Combinaison de -sC et -sV

## Commande utilisée

```bash
nmap -sC -sV 192.168.230.220
```

## Objectif

Combiner la détection des versions des services et l'exécution des scripts NSE par défaut afin d'obtenir une vue plus complète des services exposés.

## Résultat attendu

- Détection des versions des services.
- Collecte d'informations supplémentaires grâce aux scripts NSE.
- Identification plus précise de la machine cible.
```

# Combinaison des options -sC et -sV

## Commande utilisée

```bash
nmap -sC -sV 192.168.230.220
```

## Informations obtenues

- Détection des ports ouverts
- Version des services
- Certificat SSL du service RDP
- Informations NetBIOS et DNS
- Version détaillée du système
- En-tête HTTP (`Microsoft-HTTPAPI/2.0`)
- Titre de la page HTTP (`Not Found`)
- Décalage horaire du système

## Conclusion

La combinaison de `-sC` et `-sV` fournit une vue complète des services exposés. Elle permet de recueillir des informations utiles pour une phase de reconnaissance sans tenter d'exploiter la cible.

# Recherche de vulnérabilités avec NSE

## Commande utilisée

```bash
nmap -sV --script vuln -p 3389,5985 192.168.230.220
```

## Objectif

Exécuter les scripts NSE de la catégorie `vuln` sur les services RDP et WinRM afin de rechercher des indices de vulnérabilités connues.

## Précautions d’interprétation

Les résultats des scripts NSE doivent être considérés comme des indices nécessitant une validation complémentaire. L’absence de vulnérabilité détectée ne garantit pas que le système est sécurisé, et une alerte doit être confirmée en vérifiant la version du service, les correctifs installés et les conditions d’exploitation.

# Résultats du scan de vulnérabilités

## Commande utilisée

```bash
nmap -sV --script vuln -p 3389,5985 192.168.230.220
```

## Résultats

Les scripts NSE exécutés n'ont détecté aucune vulnérabilité CSRF, DOM-Based XSS ou Stored XSS sur le service HTTP associé au port 5985.

## Conclusion

Aucune vulnérabilité n'a été détectée par les scripts exécutés. Cependant, ce résultat ne garantit pas que le serveur est exempt de vulnérabilités. Les résultats doivent être complétés par d'autres outils, comme Nessus, OpenVAS ou une vérification des correctifs de sécurité.

# Découverte des scripts NSE

## Commande utilisée

```bash
ls /usr/share/nmap/scripts | head
```

## Objectif

Afficher les premiers scripts NSE installés afin de comprendre leur organisation.

## Observations

Les scripts sont classés par protocole ou par service (HTTP, SMB, RDP, SSH, DNS, etc.). Cette organisation permet de sélectionner rapidement les scripts adaptés au service que l'on souhaite analyser.

## Conclusion

Nmap intègre plusieurs centaines de scripts NSE spécialisés. Un analyste de sécurité choisit les scripts correspondant aux services détectés afin d'obtenir des informations pertinentes sans exécuter de contrôles inutiles.

# Recherche des scripts HTTP

## Commande utilisée

```bash
ls /usr/share/nmap/scripts | grep "^http-"
```

## Objectif

Lister tous les scripts NSE dédiés aux services HTTP.

## Observations

Les scripts HTTP couvrent différents besoins :

- recherche de vulnérabilités (CVE) ;
- détection de WAF ;
- analyse WebDAV ;
- analyse WordPress ;
- recherche de vulnérabilités XSS.

## Conclusion

Les scripts NSE sont organisés par protocole et par fonctionnalité. Cette organisation permet de sélectionner uniquement les scripts adaptés au service détecté, ce qui améliore l'efficacité et réduit les analyses inutiles.

# Consultation de la documentation d'un script NSE

## Commande utilisée

```bash
nmap --script-help http-title
```

## Objectif

Afficher la documentation d'un script NSE afin de comprendre son rôle, ses catégories et son fonctionnement avant son exécution.

## Observations

Le script `http-title` appartient aux catégories :

- `default`
- `discovery`
- `safe`

Il permet de récupérer le titre de la page d'accueil d'un serveur Web et suit automatiquement jusqu'à cinq redirections HTTP.

## Conclusion

Avant d'utiliser un script NSE, il est recommandé de consulter sa documentation afin de comprendre son fonctionnement, ses paramètres et son impact éventuel sur la cible.

# Utilisation du script http-title

## Commande utilisée

```bash
nmap --script http-title -p 5985 192.168.230.220
```

## Résultat

Le script n'a retourné aucun titre de page.

## Analyse

Le port 5985 héberge le service WinRM. Bien qu'il utilise le protocole HTTP, il ne fournit pas une page Web classique contenant une balise `<title>`. Le comportement observé est donc normal.

## Conclusion

Tous les scripts HTTP ne produisent pas nécessairement un résultat sur tous les services utilisant HTTP. Il est important de choisir les scripts en fonction du type exact de service analysé.

# Découverte des scripts RDP

## Commande utilisée

```bash
ls /usr/share/nmap/scripts | grep "^rdp-"
```

## Scripts disponibles

- `rdp-enum-encryption` : analyse les méthodes de chiffrement RDP.
- `rdp-ntlm-info` : collecte des informations sur le serveur RDP.
- `rdp-vuln-ms12-020` : vérifie une vulnérabilité historique de Microsoft RDP.

## Conclusion

Les scripts RDP permettent d'obtenir des informations détaillées sur un serveur Remote Desktop, d'évaluer sa configuration de sécurité et de rechercher certaines vulnérabilités connues.

# Analyse du chiffrement RDP

## Commande utilisée

```bash
nmap --script rdp-enum-encryption -p 3389 192.168.230.220
```

## Résultats

Le serveur prend en charge :

- CredSSP (Network Level Authentication)
- CredSSP avec authentification anticipée
- RDSTLS (Remote Desktop Security using TLS)

## Analyse

Ces résultats indiquent que le serveur utilise des mécanismes modernes pour protéger les connexions RDP.

## Conclusion

La présence de ces mécanismes est un indicateur positif, mais elle ne permet pas à elle seule de conclure que le serveur est entièrement sécurisé. Une évaluation complète nécessite d'autres vérifications.

# Vérification de la vulnérabilité MS12-020

## Commande utilisée

```bash
nmap --script rdp-vuln-ms12-020 -p 3389 192.168.230.220
```

## Résultat

Le script n'a détecté aucun indice indiquant que le serveur est vulnérable à la vulnérabilité Microsoft MS12-020.

## Analyse

Le script vérifie uniquement cette vulnérabilité spécifique. L'absence de résultat ne signifie pas que le service RDP est exempt de toute vulnérabilité.

## Conclusion

Le contrôle est rassurant pour MS12-020, mais une évaluation complète du service RDP nécessite d'autres vérifications et d'autres outils.
