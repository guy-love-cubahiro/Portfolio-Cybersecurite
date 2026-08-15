# Investigation SOC — Active Directory

## 1. Objectif

Cette investigation reproduit dans un environnement de laboratoire
un scénario contrôlé d'échecs d'authentification Active Directory.

L'objectif est d'utiliser les journaux de sécurité Windows pour
identifier le compte concerné, la machine source et les événements
associés au verrouillage du compte.

---

## 2. Environnement

- Domaine : `cyberlab.local`
- Contrôleur de domaine : `WIN-C11UVCJVMOD.cyberlab.local`
- Poste client : `GUYLOVE_WINDOWS`
- Adresse IPv4 du poste client : `192.168.230.218`
- Compte utilisé pour le scénario : `CYBERLAB\ngagnon`

---

## 3. Scénario

Plusieurs tentatives d'authentification incorrectes ont été générées
volontairement depuis le poste Windows 11 du laboratoire.

La politique du domaine était configurée avec un seuil de verrouillage
de cinq tentatives.

L'objectif était d'observer puis de corréler les événements de sécurité
générés par Active Directory.

---

## 4. Event ID 4740 — Account Lockout

L'événement 4740 indique qu'un compte utilisateur Active Directory
a été verrouillé.

Informations observées :

- Compte : `ngagnon`
- Domaine : `CYBERLAB`
- Ordinateur appelant : `GUYLOVE_WINDOWS`
- Heure observée : `11:58:06`

Cet événement permet d'identifier directement la machine associée
au verrouillage du compte.

---

## 5. Event ID 4771 — Kerberos Pre-Authentication Failure

Un événement 4771 a également été observé.

Informations principales :

- Compte : `ngagnon`
- Service : `krbtgt/CYBERLAB`
- Adresse IP source : `192.168.230.218`
- Port source : `51843`
- Code d'échec : `0x12`
- Heure observée : `11:58:11`

Cet événement fournit notamment l'adresse réseau permettant de
corréler l'activité avec le poste Windows 11.

---

## 6. Corrélation

La corrélation des événements fournit la chaîne suivante :

`CYBERLAB\ngagnon`
→ verrouillage du compte
→ Event ID 4740
→ ordinateur appelant `GUYLOVE_WINDOWS`
→ Event ID 4771
→ adresse source `192.168.230.218`

Les deux événements permettent donc d'associer le compte concerné,
le poste client et son adresse IP.

---

## 7. Event ID 4625

Aucun événement 4625 pertinent correspondant au scénario actuel
n'a été utilisé.

Un ancien événement 4625 présent dans le laboratoire était associé
à un problème antérieur de synchronisation temporelle et a été exclu
de l'analyse afin de ne pas introduire une preuve non pertinente.

---

## 8. Analyse SOC

Dans un environnement de production, un verrouillage de compte
nécessiterait une investigation complémentaire afin de déterminer
l'origine des échecs d'authentification.

Les hypothèses à examiner pourraient notamment inclure :

- erreur répétée de l'utilisateur ;
- ancien mot de passe enregistré sur un poste ;
- service utilisant des identifiants obsolètes ;
- tâche planifiée ;
- tentative d'accès non autorisée ;
- activité automatisée.

Les événements doivent être corrélés avant de conclure à une activité
malveillante.

---

## 9. Conclusion

Cette investigation a démontré comment les journaux de sécurité
Windows peuvent être utilisés pour analyser un verrouillage de compte
Active Directory.

Les événements 4740 et 4771 ont permis d'identifier le compte,
la machine source et l'adresse IP associée à l'activité.

Cet exercice reproduit une démarche de base utilisée dans
l'investigation d'incidents d'authentification en environnement
Active Directory.