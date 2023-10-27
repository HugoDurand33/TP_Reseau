# tp 4

## I. DHCP Client

🌞 Déterminer

```
[hugo@localhost ~]$ ipconfig /all
Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . :
   Description. . . . . . . . . . . . . . : Killer(R) Wi-Fi 6E AX1675i 160MHz Wireless Network Adapter (211NGW)
   Adresse physique . . . . . . . . . . . : 8C-F8-C5-0E-69-C4
   DHCP activé. . . . . . . . . . . . . . : Oui
   Configuration automatique activée. . . : Oui
   Adresse IPv6 de liaison locale. . . . .: fe80::b17b:a405:4c15:e394%8(préféré)
   Adresse IPv4. . . . . . . . . . . . . .: 10.33.70.204(préféré)
   Masque de sous-réseau. . . . . . . . . : 255.255.240.0
   Bail obtenu. . . . . . . . . . . . . . : ⭐vendredi 27 octobre 2023 08:53:33⭐
   Bail expirant. . . . . . . . . . . . . : ⭐samedi 28 octobre 2023 08:53:26⭐
   Passerelle par défaut. . . . . . . . . : 10.33.79.254
   Serveur DHCP . . . . . . . . . . . . . : ⭐10.33.79.254⭐
   IAID DHCPv6 . . . . . . . . . . . : 109902021
   DUID de client DHCPv6. . . . . . . . : 00-01-00-01-2B-F8-FF-74-00-E0-4C-68-BF-96
   Serveurs DNS. . .  . . . . . . . . . . : 8.8.8.8
                                       1.1.1.1
   NetBIOS sur Tcpip. . . . . . . . . . . : Activé
```

🌞 Capturer un échange DHCP

#### dossier wireshark : tp4_dhcp_client.pcapng

🌞 Analyser la capture Wireshark

parmi ces 4 trames, laquelle contient les informations proposées au client ?

    c'est la trame Offer

## II. Serveur DHCP

