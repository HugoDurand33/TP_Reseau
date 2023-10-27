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

🌞Ajouter les routes statiques nécessaires pour que john et marcel puissent se ping

commande pour marcel :
    
```
sudo ip route add 10.3.1.0/24 via 10.3.2.254 dev enp0s3
```

```
[hugo@localhost ~]$ sudo nano /etc/sysconfig/network-scripts/route-enp0s3

10.3.1.0/24 via 10.3.2.254 dev enp0s3 

[hugo@localhost ~]$ sudo systemctl restart NetworkManager
```

commande pour john :

```
sudo ip route add 10.3.2.0/24 via 10.3.1.254 dev enp0s3
```

```
[hugo@localhost ~]$ sudo nano /etc/sysconfig/network-scripts/route-enp0s3

10.3.2.0/24 via 10.3.1.254 dev enp0s3

sudo systemctl restart NetworkManager

```

###  2. Analyse de trames


sudo ip neigh flush all

sur routeur et marcel : sudo tcpdump not port 22

sur john : ping 10.3.2.12

🌞Analyse des échanges ARP

| ordre | type trame  | IP source           | MAC source                   | IP destination      | MAC destination              |
| ----- | ----------- | ------------------- | ---------------------------- | ------------------- | ---------------------------- |
| 1     | Requête ARP | x                   | `john` `08:00:27:42:4a:6f`   | x                   | Broadcast `FF:FF:FF:FF:FF`   |
| 2     | Réponse ARP | x                   | `routeur` `08:00:27:e7:0e:1a`| x                   | `john` `08:00:27:42:4a:6f`   |
| 3     | Ping        | `john` `10.3.1.11`  | `john` `08:00:27:42:4a:6f`   | `marcel` `10.3.2.12`| `routeur` `08:00:27:e7:0e:1a`|
| 3     | Ping        | `john` `10.3.1.11`  | `routeur``08:00:27:fb:fe:b0` | `marcel` `10.3.2.12`| `marcel` `08:00:27:9e:58:94` |
| 4     | Requête ARP | x                   | `marcel` `08:00:27:9e:58:94` | x                   | Broadcast `FF:FF:FF:FF:FF`   |
| 5     | Réponse ARP | x                   | `routeur` `08:00:27:fb:fe:b0`| x                   | `marcel` `08:00:27:9e:58:94` |
| 6     | Pong        | `marcel` `10.3.2.12`| `marcel` `08:00:27:9e:58:94` | `john` `10.3.1.11`  | `routeur` `08:00:27:fb:fe:b0`|
| 6     | Pong        | `marcel` `10.3.2.12`| `routeur` `08:00:27:e7:0e:1a`| `john` `10.3.1.11`  | `john` `08:00:27:42:4a:6f`   |

### 3. Accès internet

🌞Donnez un accès internet à vos machines - config routeur

```
[hugo@localhost ~]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=114 time=21.6 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=114 time=19.1 ms
^C
--- 8.8.8.8 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1003ms
```

```
[hugo@localhost ~]$ sudo firewall-cmd --add-masquerade --permanent
success
[hugo@localhost ~]$ sudo firewall-cmd --reload
success
```

🌞Donnez un accès internet à vos machines - config clients

```
[hugo@localhost ~]$ sudo nano /etc/sysconfig/network


[hugo@localhost ~]$ sudo systemctl restart NetworkManager

[hugo@localhost ~]$ sudo nano /etc/resolv.conf
```

Vérification de l'accès à Internet (depuis marcel et john) :

```
[hugo@localhost ~]$ ping google.com
PING google.com (142.250.75.238) 56(84) bytes of data.
64 bytes from par10s41-in-f14.1e100.net (142.250.75.238): icmp_seq=1 ttl=116 time=30.5 ms
64 bytes from par10s41-in-f14.1e100.net (142.250.75.238): icmp_seq=2 ttl=116 time=27.4 ms
64 bytes from par10s41-in-f14.1e100.net (142.250.75.238): icmp_seq=3 ttl=116 time=28.1 ms
^C
--- google.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2004ms
```

| ordre | type trame  | IP source           | MAC source                   | IP destination      | MAC destination              |
| ----- | ----------- | ------------------- | ---------------------------- | ------------------- | ---------------------------- |
| 1     | Ping        | `john` `10.3.1.11`  | `john` `08:00:27:42:4a:6f`   | `marcel` `10.3.2.12`| `routeur` `08:00:27:e7:0e:1a`|
| 2     | Ping        | `john` `10.3.1.11`  | `routeur` `08:00:27:fb:fe:b0`| `marcel` `10.3.2.12`| `marcel` `08:00:27:9e:58:94` |
| 3     | Pong        | `marcel` `10.3.2.12`| `marcel` `08:00:27:9e:58:94` | `john` `10.3.1.11`  | `routeur` `08:00:27:fb:fe:b0`|
| 4     | Pong        | `marcel` `10.3.2.12`| `routeur` `08:00:27:e7:0e:1a`| `john` `10.3.1.11`  | `john` `08:00:27:42:4a:6f`   |

#### dossier wireshark : tp3_LAN1.pcapng
#### dossier wireshark : tp3_LAN2.pcapng
