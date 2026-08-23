# Projet 04 --- Analyse de vulnérabilités avec OpenVAS / Greenbone

## Introduction

Ce rapport présente une évaluation de vulnérabilités réalisée dans un
environnement de laboratoire virtualisé et autorisé. La cible principale
est un serveur Windows Server 2022 utilisé comme contrôleur de domaine
Active Directory.

L'objectif est de démontrer un cycle complet de gestion des
vulnérabilités : identification, scan OpenVAS, analyse, validation avec
Nmap, priorisation, remédiation, rescan et documentation des risques
résiduels.

## Objectifs

-   Configurer et utiliser OpenVAS / Greenbone.
-   Identifier et analyser les findings sur Windows Server 2022.
-   Corréler les observations avec Nmap lorsque pertinent.
-   Prioriser selon la sévérité et le contexte.
-   Appliquer des remédiations sans perturber Active Directory.
-   Vérifier les corrections par rescan.
-   Documenter les risques résiduels.

## Architecture du laboratoire

  -----------------------------------------------------------------------
  Système                 Rôle                    Adresse IP
  ----------------------- ----------------------- -----------------------
  Kali Linux              OpenVAS / Greenbone et  192.168.230.219
                          Nmap                    

  Windows Server 2022     Cible / contrôleur de   192.168.230.220
                          domaine                 

  Windows 11              Poste client du domaine 192.168.230.218
  -----------------------------------------------------------------------

L'environnement est utilisé uniquement à des fins de laboratoire et
d'apprentissage.

## Méthodologie

1.  Validation de la cible et de la connectivité.
2.  Scan OpenVAS.
3.  Analyse des findings et du QoD.
4.  Corrélation avec Nmap lorsque pertinente.
5.  Priorisation selon la sévérité et le contexte Active Directory.
6.  Remédiations sélectionnées.
7.  Validation fonctionnelle du contrôleur de domaine.
8.  Rescan OpenVAS.
9.  Comparaison avant/après.
10. Documentation des risques résiduels.

# 1. Résultats du scan initial

### Résultat 1 --- Énumération des services DCE/RPC et MSRPC

**Hôte affecté :** 192.168.230.220 (Windows Server 2022) **Port de
détection :** 135/TCP\
**Sévérité OpenVAS :** Medium --- 5.0\
**QoD :** 80 %

OpenVAS a identifié plusieurs services DCE/RPC et MSRPC exposés par le
serveur Windows Server 2022, notamment Netlogon, LSASS, SAM, Active
Directory DRS, Windows Event Log, Print Spooler et DNS Server.

Cette détection correspond principalement à une exposition
d'informations sur les services RPC disponibles. Un attaquant disposant
d'un accès réseau au serveur pourrait utiliser ces informations lors
d'une phase de reconnaissance afin de mieux identifier la surface
d'attaque.

Dans le contexte de ce laboratoire, le serveur est situé sur un réseau
privé VMware et plusieurs de ces services sont nécessaires au
fonctionnement d'Active Directory. Le risque doit donc être interprété
en fonction du contexte réseau et du rôle du serveur.

**Remédiation recommandée :** - limiter l'accès RPC aux systèmes et
réseaux autorisés ; - utiliser le pare-feu Windows et la segmentation
réseau ; - désactiver les services inutiles lorsque cela est possible
; - maintenir Windows Server à jour ; - surveiller les accès anormaux
aux services RPC.

**Conclusion :** La détection ne démontre pas à elle seule
l'exploitation d'une vulnérabilité du serveur. Elle met principalement
en évidence une possibilité d'énumération et de reconnaissance des
services RPC exposés.

### Résultat 2 --- Protocoles TLS 1.0 et TLS 1.1 obsolètes

**Hôte affecté :** 192.168.230.220\
**Service affecté :** RDP\
**Port :** 3389/TCP\
**Sévérité OpenVAS :** Medium --- 4.3\
**QoD :** 98 %

OpenVAS a détecté que le service accessible sur le port 3389/TCP prend
en charge TLS 1.2 ou supérieur, mais accepte également les protocoles
obsolètes TLS 1.0 et TLS 1.1.

Ces anciennes versions de TLS présentent des faiblesses cryptographiques
connues et ne doivent plus être utilisées lorsque des protocoles plus
récents sont disponibles. OpenVAS référence notamment les vulnérabilités
historiques BEAST (CVE-2011-3389) et FREAK (CVE-2015-0204).

**Impact :** L'utilisation de protocoles TLS obsolètes augmente la
surface d'attaque cryptographique et peut exposer les communications à
des techniques visant à compromettre leur confidentialité.

**Remédiation recommandée :** - vérifier la compatibilité des
applications et clients ; - désactiver TLS 1.0 ; - désactiver TLS 1.1
; - conserver TLS 1.2 ou une version supérieure lorsque celle-ci est
prise en charge ; - effectuer un nouveau scan après la remédiation afin
de confirmer sa disparition.

**Conclusion :** Cette détection représente un problème de configuration
cryptographique. Le serveur prend déjà en charge une version moderne de
TLS, mais conserve également des protocoles obsolètes qui devraient être
désactivés après validation de la compatibilité des systèmes.

### Résultat 3 --- TCP Timestamps Information Disclosure

**Hôte affecté :** 192.168.230.220\
**Port :** general/tcp\
**Sévérité OpenVAS :** Low --- 2.6\
**QoD :** 80 %

OpenVAS a détecté que le serveur implémente les TCP Timestamps définis
par RFC 1323/RFC 7323.

Le scanner a observé l'évolution des valeurs de timestamp dans les
réponses TCP. Cette information peut permettre d'estimer
approximativement la durée de fonctionnement (uptime) du système.

**Impact :** Cette fonctionnalité peut divulguer une information
supplémentaire sur le système distant. Un attaquant pourrait utiliser
cette information pendant une phase de reconnaissance afin de compléter
le profil technique de la cible.

**Remédiation recommandée :** OpenVAS indique qu'il est possible de
modifier le comportement des TCP Timestamps sur Windows. Cependant, la
pertinence de cette modification doit être évaluée en fonction du
système, de son rôle et du faible niveau de risque associé à cette
détection.

**Priorité :** Faible.

**Conclusion :** Il s'agit principalement d'une divulgation
d'information et non de la preuve d'une compromission ou d'une
vulnérabilité directement exploitable. Dans le contexte du laboratoire,
cette détection présente une priorité inférieure aux problèmes liés aux
protocoles TLS obsolètes.

# 2. Remédiation TLS 1.0 / TLS 1.1 et validation

### Vulnérabilité sélectionnée

Lors du scan initial du serveur Windows Server 2022, OpenVAS a détecté
la vulnérabilité suivante :

-   **Nom :** SSL/TLS: Deprecated TLSv1.0 and TLSv1.1 Protocol Detection
-   **Sévérité :** 4.3 (Medium)
-   **QoD :** 98 %
-   **Hôte :** 192.168.230.220
-   **Port :** 3389/TCP
-   **Service concerné :** Remote Desktop Protocol (RDP)

Cette détection indiquait que le service acceptait encore les protocoles
obsolètes TLS 1.0 et TLS 1.1.

### Risque identifié

TLS 1.0 et TLS 1.1 sont des protocoles cryptographiques obsolètes.

Leur utilisation augmente la surface d'attaque et peut exposer les
communications à des faiblesses cryptographiques connues.

OpenVAS recommandait de désactiver ces versions et de privilégier TLS
1.2 ou une version supérieure.

### Remédiation

Les protocoles TLS 1.0 et TLS 1.1 ont été désactivés sur Windows Server
2022 au niveau de SCHANNEL.

Les paramètres suivants ont été appliqués :

TLS 1.0 :

-   Enabled = 0
-   DisabledByDefault = 1

TLS 1.1 :

-   Enabled = 0
-   DisabledByDefault = 1

Le serveur a ensuite été redémarré afin que les modifications soient
prises en compte.

### Validation

Un nouveau scan OpenVAS a été exécuté après la remédiation.

Le scan initial contenait trois résultats :

1.  DCE/RPC and MSRPC Services Enumeration Reporting --- 5.0 (Medium)
2.  SSL/TLS: Deprecated TLSv1.0 and TLSv1.1 Protocol Detection --- 4.3
    (Medium)
3.  TCP Timestamps Information Disclosure --- 2.6 (Low)

Après la remédiation, le nouveau scan ne contient plus que deux
résultats :

1.  DCE/RPC and MSRPC Services Enumeration Reporting --- 5.0 (Medium)
2.  TCP Timestamps Information Disclosure --- 2.6 (Low)

La vulnérabilité **SSL/TLS: Deprecated TLSv1.0 and TLSv1.1 Protocol
Detection** n'est donc plus détectée.

### Conclusion

La remédiation a été validée avec succès par un nouveau scan OpenVAS.

Cette opération démontre un cycle complet de gestion des vulnérabilités
:

**Détection → Analyse → Priorisation → Remédiation → Nouveau scan →
Validation**

Le risque associé à l'utilisation de TLS 1.0 et TLS 1.1 sur le service
concerné a été corrigé.

# 3. Analyse technique approfondie

## Synthèse technique

  ------------------------------------------------------------------------------
  Finding               Sévérité              QoD Type              État
  ------------- ---------------- ---------------- ----------------- ------------
  DCE/RPC and         5.0 Medium             80 % Exposition /      Présent
  MSRPC                                           reconnaissance    
  Services                                                          
  Enumeration                                                       
  Reporting                                                         

  SSL/TLS             4.3 Medium             98 % Mauvaise          Corrigé
  Deprecated                                      configuration     
  TLSv1.0 and                                     cryptographique   
  TLSv1.1                                                           

  TCP                    2.6 Low             80 % Divulgation       Présent
  Timestamps                                      d'information     
  Information                                                       
  Disclosure                                                        
  ------------------------------------------------------------------------------

## Exposition RPC

OpenVAS a identifié le RPC Endpoint Mapper sur TCP/135 ainsi que
plusieurs ports RPC dynamiques. L'énumération initiale faisait notamment
apparaître Netlogon, LSASS, SAM, Active Directory DRS, Windows Event
Log, Print Spooler et DNS Server.

Cette exposition peut fournir des informations utiles à une phase de
reconnaissance. Plusieurs de ces services sont toutefois nécessaires au
fonctionnement d'un contrôleur de domaine ; une fermeture globale de RPC
n'est donc pas retenue.

## Quality of Detection (QoD)

Le QoD représente le niveau de confiance associé à la méthode de
détection utilisée par Greenbone/OpenVAS. Les findings observés
présentent un QoD de 80 % pour DCE/RPC et TCP Timestamps, et de 98 %
pour TLS 1.0/1.1.

# 4. Validation et corrélation avec Nmap

## Objectif

Les résultats OpenVAS sont corrélés avec Nmap afin de vérifier
indépendamment la surface réseau exposée par Windows Server 2022.

Les analyses Nmap déjà réalisées dans les étapes précédentes sont
réutilisées lorsqu'elles fournissent déjà les preuves nécessaires, afin
d'éviter de reproduire inutilement les mêmes scans et captures.

### Analyse des protocoles SMB

Le service SMB exposé sur TCP/445 a été analysé avec Nmap afin
d'identifier les versions du protocole supportées.

Cette vérification complète l'analyse OpenVAS en permettant d'examiner
directement la configuration du service SMB.

Les protocoles effectivement observés sont documentés à partir du
résultat Nmap.

### Exposition LDAP

Les ports LDAP et Global Catalog ont été vérifiés afin de déterminer
quels services d'annuaire sont accessibles sur le contrôleur de domaine.

Cette analyse permet notamment de distinguer les services LDAP standards
des services utilisant une couche TLS lorsque ceux-ci sont configurés.

### Kerberos

Le service Kerberos exposé sur TCP/88 est cohérent avec le rôle de
contrôleur de domaine du serveur.

Sa présence constitue un service attendu de l'infrastructure Active
Directory et non, à elle seule, une vulnérabilité.

## Corrélations confirmées

  -----------------------------------------------------------------------
  Élément                 Validation              Conclusion
  ----------------------- ----------------------- -----------------------
  MSRPC 135/TCP           Confirmé précédemment   Service RPC exposé ;
                          avec Nmap               accès à contrôler

  Ports RPC dynamiques    Confirmés précédemment  Exposition cohérente
                                                  avec Windows/AD

  RDP 3389/TCP            Confirmé précédemment   Service actif ; TLS
                                                  obsolète corrigé

  TLS 1.0/1.1             Validation par rescan   Finding disparu
                          OpenVAS                 

  TCP Timestamps          Présence confirmée      Divulgation faible

  DNS 53/TCP              53/TCP ouvert ---       Cohérent avec Active
                          service DNS détecté par Directory
                          Nmap                    
  -----------------------------------------------------------------------

> **Note :** les résultats précis de Kerberos, LDAP, SMB, SMB Signing,
> LDAPS et Global Catalog n'étaient pas renseignés dans le rapport
> source. Ils ne sont pas inventés ici et pourront être ajoutés
> uniquement à partir des sorties Nmap réellement conservées.

# 5. Priorisation et décisions de traitement

## Criticité de l'actif

Le serveur joue le rôle de contrôleur de domaine et fournit des services
essentiels tels que DNS, Kerberos, LDAP, SMB, Netlogon et RPC. Toute
remédiation susceptible d'affecter ces services doit être évaluée avant
application.

## Matrice de décision

  ------------------------------------------------------------------------
  Finding                       Sévérité Décision         Justification
  ---------------- --------------------- ---------------- ----------------
  TLS 1.0 / TLS               Medium 4.3 Corriger         Protocoles
  1.1                                                     obsolètes
                                                          pouvant être
                                                          désactivés sans
                                                          supprimer RDP

  DCE/RPC et MSRPC            Medium 5.0 Atténuer         Plusieurs
                                                          services RPC
                                                          sont nécessaires
                                                          à Active
                                                          Directory

  TCP Timestamps                 Low 2.6 Accepter /       Impact limité
                                         hardening faible 
                                         priorité         

  Print Spooler           Exposition RPC Désactiver       Aucune
                                                          impression
                                                          nécessaire dans
                                                          le laboratoire
  ------------------------------------------------------------------------

# 6. Remédiation et rescan

## Désactivation de Print Spooler

Le service Print Spooler a été arrêté et son type de démarrage configuré
sur `Disabled`. Après cette modification, les services Active Directory
essentiels ont été vérifiés et le poste Windows 11 membre du domaine a
conservé sa communication avec le contrôleur de domaine.

## Décision concernant TCP Timestamps

Le finding TCP Timestamps, de sévérité 2.6 Low, est principalement une
divulgation d'information. Une modification de la pile TCP uniquement
pour supprimer ce finding n'a pas été retenue comme priorité. Le risque
est documenté comme risque résiduel faible.

## Résultat du rescan final

Le rescan final OpenVAS ne détecte plus que deux findings : - DCE/RPC
and MSRPC Services Enumeration Reporting --- 5.0 Medium ; - TCP
Timestamps Information Disclosure --- 2.6 Low.

TLS 1.0/TLS 1.1 n'est plus détecté. Dans l'énumération RPC finale
fournie, `spoolss`, `spoolsv.exe` et le service Print Spooler
n'apparaissent plus.

## Bilan final

  ------------------------------------------------------------------------
  Finding              Sévérité initiale État final       Traitement
  ---------------- --------------------- ---------------- ----------------
  DCE/RPC and                 5.0 Medium Toujours détecté Mitigation /
  MSRPC Services                                          risque résiduel
  Enumeration                                             
  Reporting                                               

  SSL/TLS:                    4.3 Medium Non détecté      Corrigé
  Deprecated                                              
  TLSv1.0 and                                             
  TLSv1.1 Protocol                                        
  Detection                                               

  TCP Timestamps                 2.6 Low Toujours détecté Risque faible
  Information                                             résiduel
  Disclosure                                              

  Print Spooler    Exposition identifiée Non détecté dans Corrigé
  exposé via RPC                         l'énumération    
                                         RPC finale       
  ------------------------------------------------------------------------

## Risques résiduels

### DCE/RPC et MSRPC

Le port TCP/135 et plusieurs services RPC restent accessibles. Le
résultat final fait notamment apparaître Netlogon, LSASS, SAM, Active
Directory DRS, DNS Server et Event Log. Le risque doit être réduit par
segmentation, règles de pare-feu, limitation de l'accès RPC, maintenance
et surveillance.

### TCP Timestamps

Le serveur continue à exposer les TCP Timestamps. Le finding reste de
faible sévérité et est documenté comme risque résiduel faible.

# 7. Conclusion générale

Ce projet a permis de réaliser un cycle complet de gestion des
vulnérabilités sur Windows Server 2022 à l'aide d'OpenVAS / Greenbone.

Le scan initial a mis en évidence trois findings principaux :
l'énumération DCE/RPC et MSRPC, la prise en charge de TLS 1.0 et TLS 1.1
sur RDP, et la divulgation d'informations liée aux TCP Timestamps.

La désactivation de TLS 1.0 et TLS 1.1 a été validée par un nouveau scan
OpenVAS. Le Print Spooler a également été désactivé sans
dysfonctionnement Active Directory observé et il n'apparaît plus dans
l'énumération RPC finale.

DCE/RPC/MSRPC reste présent en raison de services nécessaires au
contrôleur de domaine. TCP Timestamps demeure présent avec une sévérité
faible. Ces éléments sont documentés comme risques résiduels avec des
décisions adaptées au contexte.

**Identification → Analyse → Validation → Priorisation → Remédiation →
Rescan → Validation → Gestion du risque résiduel**

Une gestion efficace des vulnérabilités ne consiste pas à faire
disparaître chaque finding, mais à réduire le risque de manière mesurée
tout en préservant les fonctions essentielles du système.
