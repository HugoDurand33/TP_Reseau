# TP_1

## I. Exploration locale en solo

### 1. Affichage d'informations sur la pile TCP/IP locale

🌞 Affichez les infos des cartes réseau de votre PC 

commande : ipconfig /all

- nom, adresse MAC et adresse IP de l'interface WiFi

   Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . :
   Description. . . . . . . . . . . . . . : Killer(R) Wi-Fi 6E AX1675i 160MHz Wireless Network Adapter (211NGW)
   Adresse physique . . . . . . . . . . . : 8C-F8-C5-0E-69-C4
   DHCP activé. . . . . . . . . . . . . . : Oui
   Configuration automatique activée. . . : Oui
   Adresse IPv6 de liaison locale. . . . .: fe80::b17b:a405:4c15:e394%8(préféré)
   Adresse IPv4. . . . . . . . . . . . . .: 10.33.48.99(préféré)
   Masque de sous-réseau. . . . . . . . . : 255.255.252.0
   Bail obtenu. . . . . . . . . . . . . . : mercredi 11 octobre 2023 09:02:30
   Bail expirant. . . . . . . . . . . . . : jeudi 12 octobre 2023 09:02:17
   Passerelle par défaut. . . . . . . . . : 10.33.51.254
   Serveur DHCP . . . . . . . . . . . . . : 10.33.51.254
   IAID DHCPv6 . . . . . . . . . . . : 109902021
   DUID de client DHCPv6. . . . . . . . : 00-01-00-01-2B-F8-FF-74-00-E0-4C-68-BF-96
   Serveurs DNS. . .  . . . . . . . . . . : 10.33.10.2
                                       8.8.8.8
   NetBIOS sur Tcpip. . . . . . . . . . . : Activé

- nom, adresse MAC et adresse IP de l'interface Ethernet

    je n'ai pas de carte réseau

🌞 Affichez votre gateway

- utilisez une commande pour connaître l'adresse IP de la passerelle (ou gateway) de votre carte WiFi

    passerelle : 10.33.51.254

🌞 Déterminer la MAC de la passerelle

commande : arp -a

Interface : 10.33.48.99 --- 0x8
  Adresse Internet      Adresse physique      Type
  10.33.48.14           e4-b3-18-48-36-68     dynamique
  10.33.48.26           70-d8-23-5e-1b-c2     dynamique
  10.33.51.254          7c-5a-1c-cb-fd-a4     dynamique
  10.33.51.255          ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
  255.255.255.255       ff-ff-ff-ff-ff-ff     statique

🌞 Trouvez comment afficher les informations sur une carte IP (change selon l'OS)

```parametre > reseau et internet > Wi-fi```

### 2. Modifications des informations

🌞 Utilisez l'interface graphique de votre OS pour changer d'adresse IP :

```parametre > reseau et internet > Wi-fi > Attribution d'adresse IP```

🌞 Il est possible que vous perdiez l'accès internet. Que ce soit le cas ou non, expliquez pourquoi c'est possible de perdre son accès internet en faisant cette opération.

    car on a la même adresse IP qu'une autre personne

## II. Exploration locale en duo

🌞 Modifiez l'IP des deux machines pour qu'elles soient dans le même réseau

```parametre > reseau et internet > Wi-fi > Attribution d'adresse IP```
