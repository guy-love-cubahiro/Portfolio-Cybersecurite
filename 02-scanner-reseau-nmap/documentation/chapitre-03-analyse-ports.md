## Comparaison entre TCP Connect Scan et SYN Scan

### TCP Connect Scan (`-sT`)

Le TCP Connect Scan établit une connexion TCP complète en réalisant le Three-Way Handshake (SYN → SYN-ACK → ACK). Une fois la connexion établie, Nmap envoie un paquet RST pour la fermer immédiatement. Cette méthode est fiable mais laisse davantage de traces sur la machine cible.

### SYN Scan (`-sS`)

Le SYN Scan envoie un paquet SYN. Si le port est ouvert, le serveur répond par un SYN-ACK. Nmap envoie alors directement un paquet RST sans terminer le Three-Way Handshake. La connexion n'est donc jamais complètement établie, ce qui rend ce scan généralement plus discret.

## Analyse des règles du pare-feu Windows

Les règles de trafic entrant montrent que le service Bureau à distance (RDP) est autorisé par le pare-feu Windows Defender.

Les règles RDP sont activées et leur action est définie sur **Autoriser**, ce qui permet aux paquets TCP destinés au port 3389 d'atteindre le service RDP.

Cette configuration explique pourquoi Nmap détecte le port **3389/tcp** dans l'état **open**.

# Comparaison des principaux scans TCP

| Caractéristique | TCP Connect Scan (`-sT`) | SYN Scan (`-sS`) |
|-----------------|--------------------------|------------------|
| Nécessite les privilèges administrateur | Non | Oui (généralement) |
| Établit une connexion complète | Oui | Non |
| Termine le Three-Way Handshake | Oui | Non |
| Plus discret | Non | Oui |
| Plus visible dans les journaux | Oui | Non (généralement) |
| Utilisation principale | Compatibilité maximale | Audit et tests de sécurité |

# Commandes étudiées

```bash
nmap -sn 192.168.230.0/24
nmap 192.168.230.220
nmap -sV 192.168.230.220
sudo nmap -O 192.168.230.220
nmap -sT 192.168.230.220
sudo nmap -sS 192.168.230.220
```

# Compétences acquises

À la fin de ce chapitre, je suis capable de :

- expliquer le fonctionnement du protocole TCP ;
- décrire le Three-Way Handshake ;
- distinguer un TCP Connect Scan d'un SYN Scan ;
- interpréter les états open, closed et filtered ;
- comprendre l'influence du pare-feu sur les résultats de Nmap ;
- analyser les échanges TCP dans Wireshark ;
- expliquer le rôle des ports éphémères ;
- interpréter les résultats d'un scan dans un contexte de cybersécurité.


Pour réduire la surface d'attaque et empêcher les connexions non autorisées.
