# tp 3

## I. ARP

### 1. Echange ARP

🌞Générer des requêtes ARP

```
[hugo@localhost ~]$ ping 10.3.1.11
PING 10.3.1.11 (10.3.1.11) 56(84) bytes of data.
64 bytes from 10.3.1.11: icmp_seq=1 ttl=64 time=5.92 ms
64 bytes from 10.3.1.11: icmp_seq=2 ttl=64 time=3.10 ms
64 bytes from 10.3.1.11: icmp_seq=3 ttl=64 time=3.69 ms
64 bytes from 10.3.1.11: icmp_seq=4 ttl=64 time=6.01 ms
```

arp machine john : 

```
[hugo@localhost ~]$ ip neigh show
10.3.1.12 dev enp0s3 lladdr ⭐ 08:00:27:53:86:9e⭐  STALE
10.3.1.1 dev enp0s3 lladdr 0a:00:27:00:00:28 DELAY
```

arp machine marcel :

```
[hugo@localhost ~]$ ip neigh show
10.3.1.1 dev enp0s3 lladdr 0a:00:27:00:00:28 REACHABLE
10.3.1.11 dev enp0s3 lladdr ⭐ 08:00:27:c8:23:35⭐  REACHABLE
```

une commande pour voir la MAC de marcel dans la table ARP de john :

```
[hugo@localhost ~]$ ip neigh show 10.3.1.11
10.3.1.11 dev enp0s3 lladdr 08:00:27:c8:23:35 STALE
```

et une commande pour afficher la MAC de marcel, depuis marcel :

```
[hugo@localhost ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether ⭐ 08:00:27:53:86:9e⭐  brd ff:ff:ff:ff:ff:ff
    inet 10.3.1.12/24 brd 10.3.1.255 scope global noprefixroute enp0s3
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe53:869e/64 scope link
       valid_lft forever preferred_lft forever
```
### 2. Analyse de trames

🌞Analyse de trames

#### dossier wireshark : arp2.pcapng

## II. Routage


