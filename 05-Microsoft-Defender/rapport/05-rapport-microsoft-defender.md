# Rapport — Protection et enquête avec Microsoft Defender

## 1. Résumé exécutif

Ce projet présente l'utilisation de Microsoft Defender Antivirus dans un environnement Windows 11 de laboratoire, depuis la vérification des protections jusqu'au triage et à l'investigation d'une détection.

Une simulation contrôlée utilisant le fichier de test antivirus EICAR a permis de générer une détection sans utiliser de malware réel. L'incident a ensuite été analysé avec Windows Security, PowerShell et Windows Event Viewer.

L'investigation a permis de corréler les Event IDs 1116 et 1117, de confirmer la réussite de l'action Defender, de construire une chronologie de l'incident et de réaliser une activité de Threat Hunting. Le verdict final est **True Positive — Simulation contrôlée**, sans compromission réelle identifiée dans le cadre du laboratoire.

## 2. Environnement du laboratoire

- Système analysé : Windows 11
- Solution de sécurité : Microsoft Defender Antivirus
- Interface de sécurité : Windows Security
- Outil d'administration et d'investigation : PowerShell
- Journalisation : Windows Event Viewer
- Journal Defender : `Microsoft-Windows-Windows Defender/Operational`
- Environnement : laboratoire contrôlé

## 3. Configuration de Microsoft Defender

La configuration de Microsoft Defender a été examinée à partir de Windows Security et de PowerShell.

Les contrôles étudiés comprennent notamment la protection en temps réel, la surveillance comportementale, la protection fournie par le cloud, l'envoi automatique d'échantillons, Tamper Protection, les exclusions, les renseignements de sécurité et le Network Inspection System.

`Get-MpComputerStatus` a été utilisé pour vérifier l'état opérationnel de Defender et `Get-MpPreference` pour examiner certains paramètres de configuration. Les paramètres observés ont servi de baseline avant les simulations de détection et les activités d'investigation.

## 4. Baseline de sécurité

Une vérification initiale de Microsoft Defender Antivirus a été réalisée avant les simulations de détection et d'investigation.

Les principaux mécanismes de protection ont été examinés à partir de l'interface Sécurité Windows et de PowerShell.

### Contrôles vérifiés

- Microsoft Defender Antivirus
- Protection en temps réel
- Surveillance comportementale
- Protection des fichiers téléchargés
- Protection fournie par le cloud
- Envoi automatique d'échantillons
- Tamper Protection
- Exclusions Microsoft Defender
- Renseignements/signatures de sécurité
- Network Inspection System

### Vérification PowerShell

La commande `Get-MpComputerStatus` a été utilisée afin de vérifier l'état opérationnel des différents composants Microsoft Defender.

La commande `Get-MpPreference` a également été utilisée afin d'examiner certains paramètres de configuration, notamment les exclusions.

Cette baseline servira de référence pour comparer l'état du système avant et après les simulations de détection réalisées dans les prochaines phases du projet.

## 5. Analyse antivirus et triage initial

Plusieurs méthodes d'analyse Microsoft Defender ont été étudiées afin de comprendre leur utilisation dans un contexte de protection et d'investigation d'un poste Windows.

### Types d'analyses étudiés

- Quick Scan
- Full Scan
- Custom Scan
- Microsoft Defender Offline Scan

Une analyse rapide a été réalisée depuis l'interface Sécurité Windows puis depuis PowerShell avec `Start-MpScan`.

Une analyse personnalisée a également été effectuée sur un dossier de laboratoire afin de démontrer la possibilité de cibler un emplacement particulier.

### Vérification PowerShell

Les commandes suivantes ont été étudiées :

- `Start-MpScan`
- `Get-MpComputerStatus`
- `Get-MpThreat`
- `Get-MpThreatDetection`

Ces commandes permettent respectivement de lancer des analyses, de vérifier l'état de Microsoft Defender et examiner les informations relatives aux menaces et détections.

### Journaux Microsoft Defender

Le journal suivant a été identifié dans Windows Event Viewer :

`Applications and Services Logs > Microsoft > Windows > Windows Defender > Operational`

Les événements associés aux analyses permettent de corréler les actions réalisées avec les traces enregistrées par Windows.

### Procédure de triage initial

Lorsqu'une alerte antivirus est observée, l'investigation initiale doit notamment permettre de déterminer :

1. Quelle menace a été détectée ?
2. Quand la détection s'est-elle produite ?
3. Quel fichier ou emplacement est concerné ?
4. Quel utilisateur et quel système sont concernés ?
5. Quelle action Microsoft Defender a-t-il appliquée ?
6. La menace a-t-elle été bloquée, supprimée ou mise en quarantaine ?
7. Existe-t-il d'autres événements associés ?
8. Une investigation supplémentaire est-elle nécessaire ?

Cette méthodologie sera appliquée lors de la simulation contrôlée de détection dans la prochaine phase du projet.

## 6. Simulation contrôlée de détection

Une simulation contrôlée a été réalisée à l'aide du fichier de test antivirus EICAR.

EICAR permet de vérifier le fonctionnement d'une solution antimalware sans utiliser de logiciel malveillant réel.

Microsoft Defender a détecté le fichier de test et l'événement a été analysé à partir de plusieurs sources.

### Sources utilisées

- Windows Security — Protection History
- PowerShell — `Get-MpThreat`
- PowerShell — `Get-MpThreatDetection`
- Windows Event Viewer
- Microsoft-Windows-Windows Defender/Operational

### Chronologie

| Élément | Heure observée |
|---|---|
| Détection initiale | 19:57:36 |
| Event ID 1116 | 19:57:36 |
| Action Defender / Event ID 1117 | 19:57:41 |
| Dernier changement d'état | 19:57:41 |

### Éléments analysés

- nom de la menace ;
- heure de détection ;
- ressource affectée ;
- action Microsoft Defender ;
- résultat de l'action ;
- événements Windows associés.

Cette corrélation permet de reconstruire la chronologie de l'incident à partir de plusieurs sources indépendantes.

## 7. Triage de l'incident EICAR

### Résumé

Microsoft Defender Antivirus a généré une détection lors d'une simulation contrôlée utilisant le fichier de test EICAR.

### Classification

Type : Simulation antimalware contrôlée

Environnement : Laboratoire Windows 11

Source de détection : Microsoft Defender Antivirus

### Analyse

La détection a été vérifiée à partir de Windows Security, PowerShell et Windows Event Viewer.

Les événements Defender ont permis de confirmer la détection et d'examiner l'action appliquée par le moteur antimalware.

### Verdict

La détection correspond à une simulation volontaire réalisée dans un environnement de laboratoire.

Il ne s'agit pas d'une compromission réelle.

### Réponse

L'action appliquée par Microsoft Defender a été vérifiée et l'état final de la menace a été examiné.

Aucune désactivation des mécanismes de protection n'a été nécessaire.

### Conclusion

La simulation confirme que Microsoft Defender est capable de détecter le fichier de test utilisé et de produire des informations exploitables pour une investigation SOC.

La corrélation entre Windows Security, PowerShell et Event Viewer permet de reconstruire les principales étapes de l'événement.

## 8. Investigation des journaux Microsoft Defender

L'incident EICAR a été analysé à partir du journal opérationnel Microsoft Defender.

Journal utilisé :

`Microsoft-Windows-Windows Defender/Operational`

### Événements principaux

#### Event ID 1116 — Détection

L'événement 1116 correspond à la détection de la menace par Microsoft Defender Antivirus.

Informations observées :

- Menace : Virus:DOS/EICAR_Test_File
- Heure : 19:57:36
- Utilisateur : GuyLove_Windows\pendo
- Gravité : Grave
- Ressource : {file:_C:\Users\pendo\OneDrive\Desktop\Defender-Lab\eicar.com.txt, webfile:_C:\Users\pendo\OneDrive\Desktop\Defender-Lab\eicar.com.txt|https://secure.eicar.org/eicar.com.txt|pid:3472, ProcessStart:134320894559321954}

#### Event ID 1117 — Action

L'événement 1117 permet d'observer l'action appliquée par Microsoft Defender après la détection.

Informations observées :

- Heure : 19:57:41
- Menace : Virus:DOS/EICAR_Test_File
- Ressource : {file:_C:\Users\pendo\OneDrive\Desktop\Defender-Lab\eicar.com.txt, webfile:_C:\Users\pendo\OneDrive\Desktop\Defender-Lab\eicar.com.txt|https://secure.eicar.org/eicar.com.txt|pid:3472, ProcessStart:134320894559321954}
- Action : Quarantaine
- Résultat : True

### Corrélation

Les événements 1116 et 1117 ont été corrélés à partir de leur chronologie et des informations relatives à la menace.

Cette corrélation permet de distinguer deux phases de l'incident :

1. Détection de la menace.
2. Application d'une action de réponse par Microsoft Defender.

### Timeline SOC

| Heure | Source | Event ID | Observation |
|---|---|---:|---|
| 19:57:36 | Edge / Defender | - | Tentative d'accès au fichier EICAR |
| 19:57:36 | Defender Operational | 1116 | Détection de EICAR |
| 19:57:41 | Defender Operational | 1117 | Action appliquée |
| 19:57:41 | Protection History | - | État final vérifié |

### Investigation PowerShell

La commande `Get-WinEvent` a permis d'interroger directement le journal Microsoft Defender depuis PowerShell.

Les événements 1116 et 1117 ont également été filtrés afin de rechercher les événements contenant le terme `EICAR`.

Cette approche démontre la possibilité d'automatiser une partie de la collecte et de l'analyse des événements de sécurité.

## 9. Verdict de l'investigation

### Classification

**True Positive — Simulation contrôlée**

Microsoft Defender a correctement identifié le fichier de test EICAR comme une menace antivirus.

### Analyse

L'investigation a permis de confirmer la détection à partir de plusieurs sources :

- Microsoft Defender Protection History ;
- PowerShell ;
- `Get-MpThreatDetection` ;
- Windows Event Viewer ;
- journal Microsoft Defender Operational ;
- Event ID 1116 ;
- Event ID 1117.

### Réponse observée

Microsoft Defender a placé le fichier détecté en quarantaine.

La propriété `ActionSuccess` observée avec PowerShell était définie sur `True`, confirmant que l'action Defender associée à la détection a réussi.

### Impact

Aucune compromission réelle n'a eu lieu.

EICAR est un fichier de test antivirus utilisé volontairement dans le laboratoire afin de valider les capacités de détection et d'investigation.

### Conclusion SOC

L'incident est classé comme un **True Positive contrôlé**.

La détection était légitime du point de vue du moteur antivirus, mais l'activité faisait partie d'une simulation autorisée.

L'investigation démontre la capacité à corréler une alerte Microsoft Defender avec les événements Windows et les informations obtenues via PowerShell afin de reconstruire la chronologie d'un incident.

## 10. Investigation Microsoft Defender avec PowerShell

PowerShell a été utilisé afin de collecter et corréler les informations relatives à l'incident EICAR.

### Commandes principales

- `Get-MpComputerStatus`
- `Get-MpThreat`
- `Get-MpThreatDetection`
- `Get-WinEvent`

### Vérification de l'état Defender

`Get-MpComputerStatus` a permis de confirmer que les principales protections Microsoft Defender étaient toujours actives après la simulation contrôlée.

### Analyse de la menace

`Get-MpThreat` a été utilisé afin d'examiner les informations relatives aux menaces connues par Microsoft Defender.

`Get-MpThreatDetection` a permis d'analyser les détections enregistrées et d'extraire notamment :

- l'heure initiale de détection ;
- le dernier changement d'état ;
- le résultat de l'action ;
- les ressources affectées.

### Corrélation avec les journaux

`Get-WinEvent` a été utilisé afin de retrouver les Event IDs 1116 et 1117 dans le journal :

`Microsoft-Windows-Windows Defender/Operational`

Cette corrélation permet de relier les données de détection Microsoft Defender aux événements enregistrés par Windows.

### Conclusion

PowerShell permet d'effectuer une investigation rapide, reproductible et partiellement automatisable.

Il permet notamment de filtrer les événements, de rechercher des indicateurs précis et de construire une vue synthétique d'un incident sans dépendre exclusivement de l'interface graphique.

## 11. Protection avancée et réduction de la surface d'attaque

Microsoft Defender fournit plusieurs mécanismes permettant de réduire la surface d'attaque d'un poste Windows au-delà de la détection antivirus traditionnelle.

### Attack Surface Reduction

Les règles Attack Surface Reduction permettent de réduire l'exposition à certains comportements fréquemment utilisés lors d'attaques.

La configuration ASR du poste a été examinée avec PowerShell à l'aide de `Get-MpPreference`.

Les modes de déploiement doivent être choisis avec prudence. Dans un environnement professionnel, une phase d'audit peut permettre d'évaluer l'impact d'une règle avant son application en mode blocage.

### Controlled Folder Access

Controlled Folder Access a été étudié comme mécanisme de protection contre les modifications non autorisées de dossiers sensibles.

La configuration a été vérifiée à partir de Windows Security et PowerShell.

Un test contrôlé et non malveillant a été effectué afin d'observer le comportement de la protection.

### Journaux

Les événements Microsoft Defender ont été examinés dans :

`Microsoft-Windows-Windows Defender/Operational`

Les Event IDs liés à Controlled Folder Access peuvent fournir des informations permettant d'identifier l'application, la ressource et l'action associées à un événement de protection.

### Faux positifs

Une alerte ou un blocage ne constitue pas automatiquement la preuve d'une compromission.

L'analyste SOC doit examiner le contexte de l'activité avant de déterminer si l'événement représente un True Positive ou un False Positive.

### Recommandations de durcissement

- Maintenir Microsoft Defender et ses renseignements de sécurité à jour.
- Maintenir la protection en temps réel active.
- Maintenir Tamper Protection active.
- Contrôler rigoureusement les exclusions Defender.
- Évaluer les règles Attack Surface Reduction avant leur déploiement.
- Utiliser un mode Audit lorsque nécessaire avant de passer en Block.
- Utiliser Controlled Folder Access lorsque le contexte de l'organisation le justifie.
- Centraliser et surveiller les événements Defender.
- Investiguer les blocages avant de créer des exceptions.
- Appliquer le principe du moindre privilège.

## 12. Threat Hunting

### Hypothèse

À la suite de la détection EICAR, une recherche proactive a été réalisée afin de déterminer si d'autres événements Microsoft Defender pouvaient indiquer une activité supplémentaire autour de l'incident.

### Questions de hunting

1. Existe-t-il d'autres détections antivirus ?
2. Existe-t-il des actions Defender ayant échoué ?
3. Des modifications de configuration Defender ont-elles été observées ?
4. Existe-t-il des événements inhabituels autour de la période de l'incident ?
5. Les traces observées sont-elles cohérentes avec la simulation autorisée ?

### Résultats du Threat Hunting

Une fenêtre temporelle entourant l'incident EICAR a été définie afin de limiter l'analyse aux événements pertinents.

Les événements du journal `Microsoft-Windows-Windows Defender/Operational` ont été collectés et regroupés par Event ID.

### Recherches effectuées

- détections supplémentaires ;
- actions ou messages indiquant un échec ;
- modifications de configuration Defender ;
- événements Warning/Error ;
- recherche multi-indicateurs ;
- corrélation temporelle autour de l'incident.

### Détections supplémentaires

Aucune détection supplémentaire n'a été identifiée dans la fenêtre temporelle analysée.

### Échecs Defender

Aucun échec Defender pertinent n'a été identifié dans la fenêtre temporelle analysée.

### Modifications de configuration

Aucune modification de configuration Defender pertinente n'a été identifiée dans la fenêtre temporelle analysée.

### Événements Warning/Error

Détection EICAR — Event ID 1116 (Avertissement)

### Conclusion du hunting

Aucun autre événement suspect n'a été identifié dans l'intervalle de temps analysé.

## 13. Verdict du Threat Hunting

### Hypothèse

Une activité supplémentaire potentiellement suspecte autour de l'incident EICAR a été recherchée dans les journaux Microsoft Defender.

### Verdict

**Hypothèse non confirmée.**

Les recherches effectuées dans la fenêtre temporelle étudiée n'ont pas permis d'identifier d'élément supplémentaire indiquant une compromission au-delà de la simulation EICAR déjà connue.

Les événements observés sont cohérents avec le scénario de laboratoire et les activités Microsoft Defender analysées.

### Niveau de confiance

Modéré.

### Limites de l'analyse

Cette conclusion repose uniquement sur les sources disponibles dans le laboratoire, principalement Microsoft Defender et les journaux Windows examinés.

L'absence d'indicateur supplémentaire dans ces sources ne constitue pas une preuve absolue d'absence de compromission.

Dans un environnement SOC réel, l'analyse pourrait être enrichie avec d'autres sources telles que :

- EDR ;
- SIEM ;
- DNS ;
- proxy ;
- pare-feu ;
- authentification ;
- télémétrie réseau ;
- journaux Sysmon.

### Décision

Aucune escalade supplémentaire n'est requise dans le contexte de cette simulation contrôlée.

## 14. Résumé de l'incident principal

Une simulation contrôlée utilisant le fichier de test antivirus EICAR a permis de valider les capacités de détection et d'investigation de Microsoft Defender Antivirus.

Microsoft Defender a identifié la menace associée au fichier de test et les traces correspondantes ont été analysées dans Windows Security, PowerShell et Windows Event Viewer.

### Principales observations

- Menace : `Virus:DOS/EICAR_Test_File`
- Utilisateur : `GuyLove_Windows\Pendo`
- Event ID 1116 : détection
- Heure de détection : 19:57:36
- Event ID 1117 : action
- Heure de l'action : 19:57:41
- Action observée : quarantaine
- `ActionSuccess` : `True`
- Verdict : True Positive contrôlé
- Escalade Tier 2 : non requise

La corrélation des événements a permis d'observer environ cinq secondes entre l'événement de détection 1116 et l'événement d'action 1117.