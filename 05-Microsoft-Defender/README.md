# Projet 05 — Protection et enquête avec Microsoft Defender

## Présentation

Ce projet démontre l'utilisation de Microsoft Defender Antivirus dans un environnement Windows 11 de laboratoire, depuis la vérification des mécanismes de protection jusqu'au triage et à l'investigation d'une détection.

Une simulation contrôlée utilisant le fichier de test EICAR a été réalisée afin de générer une détection antivirus sans utiliser de malware réel.

## Objectifs

- Vérifier l'état et la configuration de Microsoft Defender.
- Réaliser des analyses antivirus.
- Générer une détection contrôlée avec EICAR.
- Examiner l'historique de protection.
- Analyser les événements Microsoft Defender.
- Utiliser PowerShell pour le triage et l'investigation.
- Corréler les Event IDs 1116 et 1117 afin de reconstruire la chronologie de l'incident.
- Étudier les mécanismes Attack Surface Reduction (ASR) et Controlled Folder Access (CFA).
- Construire un ticket d'incident SOC.
- Réaliser une activité de Threat Hunting.
- Documenter le verdict et la clôture de l'incident.

## Environnement

- Windows 11
- Microsoft Defender Antivirus
- Windows Security
- PowerShell
- Windows Event Viewer
- VMware Workstation Pro

## Scénario principal

Le fichier de test EICAR a été utilisé afin de déclencher volontairement une détection Microsoft Defender dans un environnement contrôlé.

La détection a ensuite été analysée à partir de plusieurs sources :

- Protection History ;
- `Get-MpThreat` ;
- `Get-MpThreatDetection` ;
- `Get-WinEvent` ;
- journal `Microsoft-Windows-Windows Defender/Operational`.

Les Event IDs 1116 et 1117 ont permis de corréler la détection et l'action de réponse appliquée par Microsoft Defender et de reconstruire la chronologie de l'incident.

## Résultat

Microsoft Defender a correctement détecté le fichier de test.

L'action de réponse de Microsoft Defender a réussi et, après contextualisation et corrélation des preuves, l'incident a été classé :

**True Positive — Simulation contrôlée autorisée**

Aucune compromission réelle n'a été identifiée dans le cadre du laboratoire.

## Compétences démontrées

- Microsoft Defender Antivirus
- Windows Security
- PowerShell
- Windows Event Viewer
- Analyse de journaux
- Triage SOC
- Réponse aux incidents (Incident Response)
- Threat Hunting (recherche proactive de menaces)
- Corrélation d'événements
- Construction d'une chronologie d'incident
- Priorisation des incidents
- Définition de critères d'escalade
- Documentation SOC

## Documentation

Le dossier `rapport` contient :

- le rapport technique complet ;
- le ticket d'incident SOC EICAR.

Le dossier `Images` contient les preuves techniques et les captures d'écran réalisées pendant le laboratoire.

## Sécurité

Aucun malware réel n'a été utilisé.

EICAR a été utilisé uniquement comme fichier de test afin de valider le fonctionnement de Microsoft Defender dans un environnement contrôlé.