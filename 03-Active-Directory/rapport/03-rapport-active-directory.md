# Projet 03 — Déploiement et sécurisation d'un environnement Active Directory

## 1. Résumé

Ce projet présente le déploiement, la configuration, la sécurisation
et la validation d'un environnement Microsoft Active Directory
réalisé dans un laboratoire virtualisé.

L'environnement repose sur Windows Server 2022 comme contrôleur de
domaine et Windows 11 comme poste client membre du domaine.

Le projet couvre notamment AD DS, DNS, les unités d'organisation,
les utilisateurs et groupes, les stratégies de groupe, les partages
réseau, les permissions, PowerShell, les politiques de sécurité,
Kerberos ainsi que l'analyse des journaux Windows dans un scénario
d'investigation SOC.

L'objectif est de reproduire plusieurs tâches pouvant être rencontrées
dans l'administration et la surveillance d'un environnement Active
Directory d'entreprise.

## 2. Environnement du laboratoire

### Machines virtuelles

| Machine | Rôle | Système |
|---|---|---|
| Windows Server 2022 | Contrôleur de domaine, DNS, fichiers | Windows Server 2022 |
| Windows 11 | Poste utilisateur du domaine | Windows 11 |
| Kali Linux | Machine de laboratoire complémentaire | Kali Linux |

### Réseau

Le laboratoire utilise un réseau VMware en mode NAT.

Principales adresses utilisées :

- Contrôleur de domaine : `192.168.230.220`
- Windows 11 : `192.168.230.218`
- Kali Linux : `192.168.230.219`
- Domaine DNS : `cyberlab.local`
- Nom NetBIOS : `CYBERLAB`

Le poste Windows 11 utilise le contrôleur de domaine comme serveur
DNS afin de permettre la découverte des services Active Directory.

## 3. Déploiement d'Active Directory Domain Services

Le rôle Active Directory Domain Services (AD DS) a été installé sur
Windows Server 2022.

Le serveur a ensuite été promu comme premier contrôleur de domaine
d'une nouvelle forêt :

`cyberlab.local`

Après la promotion, le bon fonctionnement d'Active Directory a été
vérifié notamment avec :

- le service NTDS ;
- le service DNS ;
- NETLOGON ;
- SYSVOL ;
- `dcdiag`.

Les tests finaux n'ont révélé aucune erreur critique empêchant le
fonctionnement du domaine.

## 4. Configuration DNS

DNS constitue un composant essentiel d'Active Directory.

Le contrôleur de domaine assure la résolution DNS pour
`cyberlab.local`.

Plusieurs tests ont été effectués :

```cmd
nslookup cyberlab.local
nslookup -type=SRV _ldap._tcp.dc._msdcs.cyberlab.local

Les enregistrements SRV permettent aux postes du domaine de localiser
les services Active Directory, notamment LDAP et le contrôleur de
domaine

## 5. Structure organisationnelle

Une structure d'unités d'organisation a été créée afin d'organiser
les objets Active Directory.

La structure comprend notamment :

- Utilisateurs ;
- Ordinateurs ;
- Groupes ;
- Serveurs ;
- départements tels que RH, Finance, TI et autres unités utilisées
  dans le laboratoire.

Cette organisation permet d'appliquer plus facilement des stratégies
de groupe et de gérer les ressources selon leur fonction.

## 6. Utilisateurs et groupes

Plusieurs comptes utilisateurs ont été créés afin de représenter
différents départements.

Des groupes globaux de sécurité ont également été configurés,
notamment des groupes suivant la convention :

`GG-*`

Les utilisateurs ont été associés aux groupes correspondant à leur
fonction.

Cette approche permet d'attribuer les permissions aux groupes plutôt
qu'individuellement aux utilisateurs.

## 7. Intégration du poste Windows 11

Le poste Windows 11 a été intégré au domaine :

`cyberlab.local`

La relation avec le domaine a été validée avec plusieurs commandes :

```cmd
whoami
echo %USERDOMAIN%
echo %LOGONSERVER%
nltest /dsgetdc:cyberlab.local
nltest /sc_verify:cyberlab.local

Le poste a ensuite été placé dans l'unité d'organisation : CYBERLAB → Ordinateurs → Postes-Utilisateurs
Le canal sécurisé avec Active Directory a été validé avec succès.

## 8. Partages réseau et permissions

Plusieurs ressources réseau ont été configurées afin de reproduire
une gestion des accès basée sur les rôles.

Des partages tels que RH et Finance ont été utilisés pour vérifier
les autorisations.

Les tests ont permis de confirmer qu'un utilisateur autorisé pouvait
accéder à la ressource correspondant à son groupe alors qu'une
ressource non autorisée retournait un refus d'accès.

Cette configuration illustre le principe du moindre privilège et
l'utilisation des groupes Active Directory pour gérer les accès.

## 9. Stratégies de groupe

Plusieurs stratégies de groupe ont été utilisées dans le laboratoire,
notamment :

- `GPO-RH-Securite`
- `GPO-Postes-Securite`
- `Default Domain Policy`
- `Default Domain Controllers Policy`

`GPO-Postes-Securite` a été liée à l'OU contenant le poste
Windows 11.

Son application a été validée avec :

```cmd
gpupdate /force
gpresult /r

Le résultat a confirmé que GPO-Postes-Securite était effectivement appliquée au poste GUYLOVE_WINDOWS


## 10. Politique de mots de passe et verrouillage

La politique du domaine a été configurée avec plusieurs paramètres
de sécurité :

| Paramètre | Valeur |
|---|---:|
| Longueur minimale | 12 caractères |
| Complexité | Activée |
| Historique | 24 mots de passe |
| Âge minimal | 1 jour |
| Âge maximal | 42 jours |
| Seuil de verrouillage | 5 tentatives |
| Durée du verrouillage | 15 minutes |
| Fenêtre d'observation | 15 minutes |
| Chiffrement réversible | Désactivé |

Le fonctionnement du verrouillage a été testé avec un compte
utilisateur du laboratoire.

## 11. Administration avec PowerShell

Une partie du projet a été consacrée à l'administration Active
Directory avec PowerShell.

Parmi les cmdlets utilisées :

```powershell
Get-ADUser
Get-ADGroup
Get-ADGroupMember
Get-ADPrincipalGroupMembership
Search-ADAccount
Disable-ADAccount
Enable-ADAccount
Set-ADUser
Add-ADGroupMember
Remove-ADGroupMember
Get-ADDefaultDomainPasswordPolicy

Ces commandes ont permis d'inventorier, modifier et contrôler différents objets Active Directory sans dépendre exclusivement de l'interface graphique

## 12. Sécurité du poste client

L'état de Microsoft Defender et du pare-feu Windows a été vérifié
sur le poste Windows 11.

Les contrôles ont confirmé :

- antivirus actif ;
- protection en temps réel active ;
- protection antispyware active ;
- pare-feu actif ;
- profil réseau reconnu comme `DomainAuthenticated`.

Ces vérifications permettent de confirmer que le poste conserve
ses protections principales après son intégration au domaine.

## 13. Audit de sécurité

L'audit Windows a été configuré et vérifié pour plusieurs catégories
pertinentes, notamment :

- ouvertures de session ;
- verrouillage de comptes ;
- gestion des utilisateurs ;
- gestion des groupes de sécurité ;
- authentification Kerberos ;
- validation des informations d'identification.

Les journaux Windows ont ensuite été utilisés pour observer les événements générés par différentes opérations Active Directory.

## 14. Investigation SOC

Un scénario contrôlé d'échecs d'authentification a été réalisé contre
le compte `CYBERLAB\ngagnon`.

L'analyse a notamment permis d'observer :

### Event ID 4740

L'événement a confirmé le verrouillage du compte et identifié :

`CallerComputerName : GUYLOVE_WINDOWS`

### Event ID 4771

Un événement Kerberos a permis d'associer l'activité à :

`192.168.230.218`

La corrélation des événements a permis d'établir une relation entre :

`ngagnon → GUYLOVE_WINDOWS → 192.168.230.218`

Le détail de cette analyse est disponible dans :

`03-investigation-soc-ad.md`

## 15. Problèmes rencontrés et dépannage

Plusieurs problèmes ont été rencontrés pendant le laboratoire.

### Synchronisation temporelle

Un problème de différence d'heure entre le poste client et le
contrôleur de domaine a affecté certaines authentifications Kerberos.

La synchronisation temporelle a été vérifiée et corrigée avant de
reprendre l'analyse des événements.

### Application d'une GPO

`GPO-Postes-Securite` ne s'appliquait initialement pas au poste.

L'analyse de `gpresult /r` a montré que l'objet ordinateur
`GUYLOVE_WINDOWS` se trouvait dans le conteneur `Computers` au lieu
de l'OU `Postes-Utilisateurs`.

Après déplacement de l'objet dans la bonne OU et exécution de :

```cmd
gpupdate /force

la stratégie est apparue correctement dans : 
gpresult /r

Authentification et diagnostic AD

Plusieurs outils ont été utilisés pour distinguer les problèmes DNS,
Kerberos et de relation avec le domaine

nslookup
nltest
klist
gpresult
dcdiag

Cette démarche a permis d'éviter des modifications inutiles de l'infrastructure et d'isoler les causes réelles des problèmes

## 16. Validation finale

Les contrôles finaux ont confirmé :

- fonctionnement d'AD DS ;
- fonctionnement DNS ;
- présence de SYSVOL et NETLOGON ;
- résolution du domaine ;
- découverte du contrôleur de domaine ;
- fonctionnement de Kerberos ;
- canal sécurisé du poste ;
- application des GPO ;
- fonctionnement des utilisateurs et groupes ;
- fonctionnement des permissions ;
- politique de verrouillage ;
- audit de sécurité ;
- protection Defender et pare-feu ;
- absence d'erreur critique lors du contrôle final d'Active Directory.

Le laboratoire est donc fonctionnel et cohérent avec les objectifs
du projet.

## 17. Compétences démontrées

Ce projet m'a permis de mettre en pratique :

- Windows Server 2022 ;
- Active Directory Domain Services ;
- DNS ;
- gestion des OU ;
- utilisateurs et groupes ;
- stratégies de groupe (GPO) ;
- permissions et partages réseau ;
- PowerShell Active Directory ;
- Kerberos ;
- politiques de mots de passe ;
- verrouillage de comptes ;
- audit Windows ;
- analyse des journaux de sécurité ;
- investigation d'incidents d'authentification ;
- dépannage Active Directory ;
- validation et documentation d'une infrastructure de laboratoire.

## 18. Conclusion

Ce laboratoire a permis de construire un environnement Active
Directory complet, de le sécuriser, de tester son fonctionnement
et d'utiliser ses journaux dans un scénario d'investigation.

Le projet combine ainsi administration système, sécurité Windows
et analyse SOC dans un environnement Active Directory contrôlé.
