# TP_2

## I. Setup IP

🌞 Mettez en place une configuration réseau fonctionnelle entre les deux machines

    mon IP: 192.168.247.133/30
    IP machine Virtuel: 192.168.247.134/30
    adresse  de réseau : 192.168.247.132
    address de broadcast 192.168.247.135

 vous renseignerez aussi les commandes utilisées pour définir les adresses IP via la ligne de commande

    netsh interface ipv4 set address name="Nom de votre interface" static Adresse IP Masque de Sous Réseau Passerelle

🌞 Prouvez que la connexion est fonctionnelle entre les deux machines

```ping 192.168.247.134```

    Envoi d’une requête 'Ping'  192.168.247.135 avec 32 octets de données :
    Réponse de 192.168.247.135 : octets=32 temps=2 ms TTL=64
    Réponse de 192.168.247.135 : octets=32 temps=1 ms TTL=64
    Réponse de 192.168.247.135 : octets=32 temps=1 ms TTL=64
    Réponse de 192.168.247.135 : octets=32 temps=1 ms TTL=64

    Statistiques Ping pour 192.168.247.135:
        Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
    Durée approximative des boucles en millisecondes :
        Minimum = 1ms, Maximum = 2ms, Moyenne = 1ms

🌞 Wireshark it

```ping 192.168.247.134```


