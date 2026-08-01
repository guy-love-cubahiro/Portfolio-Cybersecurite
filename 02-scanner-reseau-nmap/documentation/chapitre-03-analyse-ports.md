## Comparaison entre TCP Connect Scan et SYN Scan

### TCP Connect Scan (`-sT`)

Le TCP Connect Scan établit une connexion TCP complète en réalisant le Three-Way Handshake (SYN → SYN-ACK → ACK). Une fois la connexion établie, Nmap envoie un paquet RST pour la fermer immédiatement. Cette méthode est fiable mais laisse davantage de traces sur la machine cible.

### SYN Scan (`-sS`)

Le SYN Scan envoie un paquet SYN. Si le port est ouvert, le serveur répond par un SYN-ACK. Nmap envoie alors directement un paquet RST sans terminer le Three-Way Handshake. La connexion n'est donc jamais complètement établie, ce qui rend ce scan généralement plus discret.

## Analyse des règles du pare-feu Windows

Les règles de trafic entrant montrent que le service Bureau à distance (RDP) est autorisé par le pare-feu Windows Defender.

Les règles RDP sont activées et leur action est définie sur **Autoriser**, ce qui permet aux paquets TCP destinés au port 3389 d'atteindre le service RDP.

Cette configuration explique pourquoi Nmap détecte le port **3389/tcp** dans l'état **open**.

