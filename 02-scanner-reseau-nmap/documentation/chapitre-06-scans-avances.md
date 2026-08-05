# Chapitre 6 - Analyse SMB avec les scripts NSE

## Objectif

Découvrir les scripts SMB disponibles dans Nmap et apprendre à sélectionner ceux qui sont adaptés à un audit de sécurité.

## Commande utilisée

```bash
ls /usr/share/nmap/scripts | grep "^smb-"
```

## Observations

Les scripts SMB sont répartis en plusieurs catégories :

- Énumération (`smb-enum-*`)
- Informations système (`smb-os-discovery`, `smb-system-info`)
- Configuration de sécurité (`smb-security-mode`, `smb-protocols`)
- Recherche de vulnérabilités (`smb-vuln-*`)
- Scripts plus intrusifs (`smb-brute`, `smb-psexec`)

## Conclusion

SMB dispose d'un grand nombre de scripts NSE car il est un protocole essentiel dans les environnements Windows. Les scripts permettent de réaliser des audits très détaillés tout en choisissant le niveau d'intrusion adapté au contexte.

# Test du script smb-os-discovery

## Commande utilisée

```bash
nmap --script smb-os-discovery -p 445 192.168.230.220
```

## Résultat

Le port 445 est apparu à l'état `filtered` et le script n'a retourné aucune information sur le système.

## Analyse

Le pare-feu ou un mécanisme de filtrage empêche la communication avec le service SMB. Sans réponse du service, le script ne peut pas récupérer les informations du système.

## Conclusion

Les scripts NSE dépendent de l'accessibilité du service ciblé. Lorsque le port est filtré, les capacités d'énumération sont fortement limitées.

# Analyse du mode de sécurité SMB

## Commande utilisée

```bash
nmap --script smb-security-mode -p 445 192.168.230.220
```

## Résultat

Le port 445 est resté à l'état `filtered` et le script n'a retourné aucune information.

## Analyse

Le script nécessite une communication avec le service SMB pour récupérer les paramètres de sécurité. Comme le port est filtré, cette communication est bloquée par le pare-feu ou un mécanisme de sécurité.

## Conclusion

Lorsque le service SMB n'est pas accessible, les scripts d'analyse de sa configuration ne peuvent pas fournir de résultats.

# Vérification de la vulnérabilité MS17-010

## Commande utilisée

```bash
nmap --script smb-vuln-ms17-010 -p 445 192.168.230.220
```

## Résultat

Le port 445 est apparu à l'état `filtered` et le script n'a retourné aucune information concernant la vulnérabilité MS17-010.

## Analyse

Le script n'a pas pu communiquer avec le service SMB, car le pare-feu ou un mécanisme de sécurité bloque les requêtes sur le port 445.

## Conclusion

L'absence de résultat ne permet pas de conclure que le serveur est protégé contre MS17-010. Elle indique uniquement que le test n'a pas pu être réalisé dans les conditions actuelles.
