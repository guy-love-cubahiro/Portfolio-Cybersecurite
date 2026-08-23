# Projet 04 — Analyse de vulnérabilités avec OpenVAS / Greenbone

## Présentation

Ce projet présente une évaluation de vulnérabilités réalisée avec
**OpenVAS / Greenbone** sur un serveur **Windows Server 2022** utilisé
comme contrôleur de domaine Active Directory dans un environnement de
laboratoire virtualisé.

L'objectif était de réaliser un cycle complet de gestion des vulnérabilités :

**Scan → Analyse → Validation → Priorisation → Remédiation → Rescan → Gestion du risque résiduel**

---

## Objectifs

- Déployer et configurer OpenVAS / Greenbone sur Kali Linux
- Configurer une cible Windows Server 2022
- Réaliser un scan de vulnérabilités
- Analyser et contextualiser les findings OpenVAS
- Interpréter les scores de sévérité et le Quality of Detection (QoD)
- Corréler certains résultats avec Nmap
- Prioriser les risques selon le contexte du serveur
- Appliquer des mesures de remédiation
- Vérifier le fonctionnement d'Active Directory après modification
- Effectuer un rescan de validation
- Comparer les résultats avant et après remédiation
- Documenter les risques résiduels

---

## Environnement du laboratoire

| Système | Rôle | Adresse IP |
|---|---|---|
| Kali Linux | OpenVAS / Greenbone et Nmap | `192.168.230.219` |
| Windows Server 2022 | Cible / Contrôleur de domaine | `192.168.230.220` |
| Windows 11 | Poste client Active Directory | `192.168.230.218` |

L'environnement fonctionne dans **VMware Workstation** sur un réseau
de laboratoire isolé.

---

## Outils utilisés

- OpenVAS / Greenbone Vulnerability Management
- Nmap
- Kali Linux
- Windows Server 2022
- Active Directory
- Windows PowerShell
- VMware Workstation
- Git / GitHub

---

## Principaux findings

Le scan initial a notamment identifié :

| Finding | Sévérité | QoD |
|---|---:|---:|
| DCE/RPC and MSRPC Services Enumeration Reporting | 5.0 Medium | 80 % |
| SSL/TLS: Deprecated TLSv1.0 and TLSv1.1 Protocol Detection | 4.3 Medium | 98 % |
| TCP Timestamps Information Disclosure | 2.6 Low | 80 % |

L'analyse a montré que les findings ne devaient pas être traités uniquement
selon leur score de sévérité. Le rôle du serveur, l'exposition réseau,
les dépendances Active Directory et l'impact potentiel d'une remédiation
ont également été pris en compte.

---

## Validation avec Nmap

Nmap a été utilisé comme outil complémentaire afin de valider et
contextualiser certains éléments observés avec OpenVAS.

Les vérifications ont notamment permis de confirmer :

- l'exposition de MSRPC sur `135/TCP`
- la présence de ports RPC dynamiques
- le service RDP sur `3389/TCP`
- le service DNS sur `53/TCP`
- la présence des TCP Timestamps

Cette corrélation permet de ne pas dépendre exclusivement des résultats
d'un scanner automatisé.

---

## Remédiations appliquées

### Désactivation de TLS 1.0 et TLS 1.1

OpenVAS avait identifié les protocoles TLS 1.0 et TLS 1.1 sur le service
RDP.

Les protocoles obsolètes ont été désactivés sur Windows Server à l'aide
de la configuration SCHANNEL.

Un nouveau scan OpenVAS a confirmé la disparition du finding.

**Résultat : corrigé.**

### Durcissement du Print Spooler

Le service Print Spooler avait été identifié dans l'énumération RPC.

Aucune fonctionnalité d'impression n'étant nécessaire sur le contrôleur
de domaine du laboratoire, le service a été arrêté et désactivé.

Les fonctions essentielles d'Active Directory ont ensuite été vérifiées.

Le Print Spooler n'apparaît plus dans l'énumération RPC du scan final.

**Résultat : corrigé.**

---

## Résultats avant / après

| Finding | État initial | État final | Traitement |
|---|---|---|---|
| DCE/RPC / MSRPC Enumeration | Medium 5.0 | Toujours détecté | Mitigation / risque résiduel |
| TLS 1.0 / TLS 1.1 | Medium 4.3 | Non détecté | Corrigé |
| TCP Timestamps | Low 2.6 | Toujours détecté | Risque faible résiduel |
| Print Spooler via RPC | Présent | Non détecté | Corrigé |

---

## Gestion des risques résiduels

Le scan final détecte toujours l'exposition DCE/RPC/MSRPC ainsi que les
TCP Timestamps.

La suppression globale de RPC n'a pas été retenue, car plusieurs services
RPC sont nécessaires au fonctionnement du contrôleur de domaine.

Le traitement recommandé repose notamment sur :

- la segmentation réseau
- le filtrage par pare-feu
- la limitation de l'accès RPC aux systèmes autorisés
- la maintenance et l'application des correctifs
- la surveillance des communications anormales

Le finding TCP Timestamps est documenté comme un risque résiduel de faible
sévérité.

---

## Compétences démontrées

- Vulnerability Assessment
- Vulnerability Management
- OpenVAS / Greenbone
- Nmap
- Analyse de vulnérabilités
- Analyse CVSS
- Quality of Detection (QoD)
- Windows Server Hardening
- Active Directory
- PowerShell
- Priorisation des risques
- Remédiation
- Validation post-remédiation
- Gestion du risque résiduel
- Documentation technique
- Troubleshooting Linux / OpenVAS

---

## Documentation

Le rapport technique détaillé du projet est disponible ici :

[`04-rapport-openvas.md`](./04-rapport-openvas.md)

Les preuves techniques et captures d'écran sont disponibles dans :

[`Images/`](./Images/)

---

## Conclusion

Ce projet démontre qu'une analyse de vulnérabilités ne consiste pas
simplement à lancer un scanner et à corriger toutes les alertes détectées.

Chaque finding doit être validé, contextualisé et priorisé selon le rôle
de l'actif et l'impact potentiel de la remédiation.

Les mesures appliquées ont permis de supprimer la prise en charge de
TLS 1.0/TLS 1.1 et de réduire la surface d'attaque liée au Print Spooler,
tout en maintenant les fonctions essentielles d'Active Directory.

Les risques qui ne pouvaient pas être supprimés sans impact opérationnel
ont été documentés et associés à des mesures de mitigation.

---

## Cadre d'utilisation

Toutes les analyses et manipulations présentées dans ce projet ont été
réalisées dans un **environnement de laboratoire personnel et autorisé**
à des fins d'apprentissage et de démonstration professionnelle.
