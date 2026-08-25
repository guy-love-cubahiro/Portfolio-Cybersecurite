# Ticket d'incident SOC — Détection Microsoft Defender

## Résumé exécutif

Microsoft Defender Antivirus a détecté le fichier de test EICAR lors d'une simulation de sécurité contrôlée sur un poste Windows 11.

L'investigation SOC Tier 1 a corrélé les données Microsoft Defender, PowerShell et Windows Event Viewer afin de confirmer la détection et la réponse appliquée.

L'activité a été identifiée comme autorisée et aucune compromission réelle n'a été observée.

L'incident a été classé **True Positive — Simulation contrôlée autorisée**. Après contextualisation, sa priorité opérationnelle a été abaissée à P4 et l'incident a été clôturé sans escalade vers le Tier 2.

## Informations générales

- Incident ID : SOC-2026-001
- Source : Microsoft Defender Antivirus
- Type : Malware / Antivirus Detection
- Statut : Clôturé
- Environnement : Laboratoire
- Analyste : SOC Tier 1

## Alerte

- Menace : Virus:DOS/EICAR_Test_File
- Produit : Microsoft Defender Antivirus
- Gravité Defender : Grave
- Utilisateur : GuyLove_Windows\Pendo
- Heure de détection : 19:57:36
- Event ID de détection : 1116
- Event ID d'action : 1117

## Statut actuel

**True Positive — Simulation contrôlée autorisée.**

## Matrice des preuves

| Source | Preuve | Interprétation |
|---|---|---|
| Microsoft Defender | `Virus:DOS/EICAR_Test_File` | Menace identifiée |
| Protection History | Détection EICAR | Confirmation graphique de l'incident |
| `Get-MpThreat` | Menace présente | Confirmation PowerShell |
| `Get-MpThreatDetection` | `ActionSuccess = True` | Action Defender réussie |
| Resource | `eicar.com.txt` | Fichier concerné identifié |
| Web Resource | `secure.eicar.org` | Origine du fichier identifiée |
| Event ID 1116 | Détection | Confirmation dans les journaux |
| Event ID 1117 | Quarantaine | Action de réponse enregistrée |
| Timeline | 1116 → 1117 | Détection et réponse corrélées |

## Priorisation SOC

### Priorité initiale

P2 — Élevée

Justification : Microsoft Defender a signalé une détection antivirus de gravité élevée. Une priorité SOC initiale P2 a été attribuée dans le cadre de cet exercice afin de déclencher une investigation avant contextualisation.

### Priorité après investigation

P4 — Faible

### Justification

L'investigation a confirmé que l'activité provenait d'une simulation EICAR autorisée dans un environnement de laboratoire.

Microsoft Defender a correctement détecté la menace et l'action de réponse a réussi.

Aucune compromission réelle n'a été identifiée.

### Conclusion

La priorité de l'incident a été réduite après contextualisation et corrélation des preuves.

## Chaîne de l'incident

1. Accès au site officiel EICAR.
2. Tentative de téléchargement de `eicar.com.txt`.
3. Microsoft Defender détecte le fichier de test.
4. Event ID 1116 enregistré.
5. La menace est classée par Defender.
6. Une action de quarantaine est appliquée.
7. Event ID 1117 enregistré.
8. L'action Defender est confirmée avec `ActionSuccess = True`.
9. L'incident est analysé par le SOC.
10. Le contexte EICAR est confirmé.
11. L'incident est classé **True Positive — Simulation contrôlée autorisée**.
12. L'incident est clôturé.

## Matrice d'escalade

| Critère | Résultat | Escalade |
|---|---|---|
| Malware réel confirmé | Non | Non |
| Menace toujours active | Non observé | Non |
| Action Defender échouée | Non | Non |
| Compromission confirmée | Non | Non |
| Exécution malveillante confirmée | Non observée | Non |
| Persistance détectée | Non observée | Non |
| Propagation latérale | Non observée | Non |
| Activité autorisée | Oui | Non |
| Simulation connue | Oui | Non |

### Décision

**Escalade Tier 2 : Non requise**

L'incident peut être clôturé par le SOC Tier 1.

## Critères qui auraient déclenché une escalade

Une escalade vers un analyste Tier 2 aurait été envisagée notamment dans les situations suivantes :

- malware réel confirmé ;
- exécution malveillante confirmée ;
- échec de la quarantaine ou de la suppression ;
- menace toujours active ;
- persistance détectée ;
- plusieurs systèmes concernés ;
- propagation latérale suspectée ;
- activité réseau malveillante ;
- compromission de comptes ;
- privilèges élevés obtenus par l'attaquant ;
- origine de l'incident inconnue ;
- indicateurs supplémentaires nécessitant une investigation approfondie.

## Recommandations post-incident

### Microsoft Defender

- Maintenir la protection en temps réel activée.
- Maintenir Tamper Protection activée.
- Maintenir les renseignements de sécurité Defender à jour.
- Surveiller régulièrement les exclusions Defender.
- Examiner les événements Microsoft Defender Operational.

### Journalisation

- Centraliser les événements Defender dans un SIEM.
- Surveiller particulièrement les événements de détection et de réponse.
- Conserver une période de rétention adaptée aux besoins d'investigation.

### Investigation

- Corréler les alertes avec plusieurs sources avant de clôturer un incident.
- Vérifier l'utilisateur, l'hôte, la ressource et l'origine.
- Documenter les horodatages importants.
- Vérifier le résultat des actions automatiques de l'EDR / antivirus.

### Réponse

- Définir des critères d'escalade clairs.
- Documenter toutes les décisions prises par l'analyste.
- Éviter de créer des exclusions sans investigation préalable.

## Notes de clôture

Microsoft Defender Antivirus a détecté `Virus:DOS/EICAR_Test_File` sur le poste Windows 11 du laboratoire.

L'investigation Tier 1 a confirmé que la ressource correspondait au fichier de test antivirus EICAR provenant du site officiel EICAR.

Les Event IDs 1116 et 1117 ont permis de corréler la détection et l'action Microsoft Defender.

L'action de réponse a été confirmée comme réussie.

Après contextualisation, l'activité a été identifiée comme une simulation de sécurité volontaire et autorisée.

Aucune compromission réelle n'a été identifiée dans le cadre de l'investigation.

Verdict : **True Positive — Simulation contrôlée autorisée**.

Priorité finale : P4 — Faible.

Escalade Tier 2 : Non requise.

Statut : **Clôturé**.

---

## Statut final

- **Statut de l'incident :** Clôturé
- **Verdict :** True Positive — Simulation contrôlée autorisée
- **Priorité finale :** P4 — Faible
- **Escalade Tier 2 :** Non requise
- **Confinement / réponse :** Réussi
- **Niveau d'analyse :** SOC Tier 1
