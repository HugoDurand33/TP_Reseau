# TP 2

## SETUP IP

#### 🌞 Mettez en place une configuration réseau fonctionnelle entre les deux machines

Mon IP : 10.33.172.253
L'IP de la seconde machine : 10.33.172.254 
L'adresse réseau : 10.33.172.252
Adresse BroadCast 10.33.172.255


#### 🌞 Prouvez que la connexion est fonctionnelle entre les deux machines

``` ping 10.33.172.254```
Envoi d’une requête 'Ping'  10.33.172.254 avec 32 octets de données :
Réponse de 10.33.172.254 : octets=32 temps<1ms TTL=128
Réponse de 10.33.172.254 : octets=32 temps<1ms TTL=128
Réponse de 10.33.172.254 : octets=32 temps<1ms TTL=128
Réponse de 10.33.172.254 : octets=32 temps<1ms TTL=128


#### dossier wireshark : ping1.pcapng


#### 🌞 Check the ARP table

``` arp -a ```

Interface : 10.33.172.253 --- 0xd
  Adresse Internet      Adresse physique      Type
  10.33.172.254        🌟 b4-2e-99-c1-96-6e 🌟    dynamique
  10.33.172.255         ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  224.0.0.253           01-00-5e-00-00-fd     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
  255.255.255.255       ff-ff-ff-ff-ff-ff     statique

Interface : 192.168.1.168 --- 0x14
  Adresse Internet      Adresse physique      Type
  192.168.1.1           20-54-fa-24-ef-26     dynamique
  192.168.1.255         ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  224.0.0.253           01-00-5e-00-00-fd     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
  255.255.255.255       ff-ff-ff-ff-ff-ff     statique


 #### 🌞 Manipuler la table ARP

 ```netsh interface IP delete arpcache```

```arp -a```

-après commande

Interface : 10.33.172.253 --- 0xd
  Adresse Internet      Adresse physique      Type
  10.33.172.255         ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  255.255.255.255       ff-ff-ff-ff-ff-ff     statique```

 ```arp -a```

-après ping

Interface : 10.33.172.253 --- 0xd
  Adresse Internet      Adresse physique      Type
  10.33.172.254         b4-2e-99-c1-96-6e     dynamique
  10.33.172.255         ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.253           01-00-5e-00-00-fd     statique
  255.255.255.255       ff-ff-ff-ff-ff-ff     statique


  #### dossier wireskark : arp.pcapng

  ## 3 DHCP

  #### dossier wireshark : DHCP.pcapng