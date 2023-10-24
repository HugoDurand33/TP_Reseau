# tp 3

## I. ARP

### 1. Echange ARP

🌞Générer des requêtes ARP

```
[hugo@localhost ~]$ ping 10.3.1.11
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

```
[hugo@localhost ~]$ ip neigh show 10.3.1.11
10.3.1.11 dev enp0s3 lladdr 08:00:27:c8:23:35 STALE
```

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



