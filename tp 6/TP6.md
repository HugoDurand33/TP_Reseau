# TP6 : Un LAN maîtrisé meo ?

## I. Setup routeur

## II. Serveur DNS

### 1. Présentation

### 2. Setup

🌞 Dans le rendu, je veux

- un cat des 3 fichiers de conf (fichier principal + deux fichiers de zone)

```
[hugo@dns var]$ sudo cat /etc/named.conf
//
// named.conf
//
// Provided by Red Hat bind package to configure the ISC BIND named(8) DNS
// server as a caching only nameserver (as a localhost DNS resolver only).
//
// See /usr/share/doc/bind*/sample/ for example named configuration files.
//

options {
        listen-on port 53 { 127.0.0.1; any; };
        listen-on-v6 port 53 { ::1; };
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        secroots-file   "/var/named/data/named.secroots";
        recursing-file  "/var/named/data/named.recursing";
        allow-query     { localhost; any; };
        allow-query-cache { localhost; any; };

        /*
         - If you are building an AUTHORITATIVE DNS server, do NOT enable recursion.
         - If you are building a RECURSIVE (caching) DNS server, you need to enable
           recursion.
         - If your recursive DNS server has a public IP address, you MUST enable access
           control to limit queries to your legitimate users. Failing to do so will
           cause your server to become part of large scale DNS amplification

           attacks. Implementing BCP38 within your network would greatly
           reduce such attack surface
        */
        recursion yes;

        dnssec-validation yes;

        managed-keys-directory "/var/named/dynamic";
        geoip-directory "/usr/share/GeoIP";

        pid-file "/run/named/named.pid";
        session-keyfile "/run/named/session.key";

        /* https://fedoraproject.org/wiki/Changes/CryptoPolicy */
        include "/etc/crypto-policies/back-ends/bind.config";
};

logging {
        channel default_debug {
                file "data/named.run";
                severity dynamic;
        };
};

zone "tp6.b1" IN {
     type master;
     file "tp6.b1.db";
     allow-update { none; };
     allow-query {any; };
};

zone "1.4.10.in-addr.arpa" IN {
     type master;
     file "tp6.b1.rev";
     allow-update { none; };
     allow-query { any; };
};

include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";
```

```
[hugo@dns var]$ sudo cat /var/named/tp6.b1.db
$TTL 86400
@ IN SOA dns.tp6.b1. admin.tp6.b1. (
    2019061800 ;Serial
    3600 ;Refresh
    1800 ;Retry
    604800 ;Expire
    86400 ;Minimum TTL
)

; Infos sur le serveur DNS lui même (NS = NameServer)
@ IN NS dns.tp6.b1.

; Enregistrements DNS pour faire correspondre des noms à des IPs
dns       IN A 10.6.1.101
john      IN A 10.6.1.11
```

```
[hugo@dns var]$ sudo cat /var/named/tp6.b1.rev
$TTL 86400
@ IN SOA dns.tp6.b1. admin.tp6.b1. (
    2019061800 ;Serial
    3600 ;Refresh
    1800 ;Retry
    604800 ;Expire
    86400 ;Minimum TTL
)

; Infos sur le serveur DNS lui même (NS = NameServer)
@ IN NS dns.tp6.b1.

; Reverse lookup
101 IN PTR dns.tp6.b1.
11 IN PTR john.tp6.b1.
```

- un systemctl status named qui prouve que le service tourne bien

```
[hugo@dns var]$ sudo systemctl status named
● named.service - Berkeley Internet Name Domain (DNS)
     Loaded: loaded (/usr/lib/systemd/system/named.service; enabled; preset: disabled)
     Active: active (running) since Fri 2023-11-17 10:10:11 CET; 9min ago
   Main PID: 4798 (named)
      Tasks: 5 (limit: 4604)
     Memory: 17.9M
        CPU: 142ms
     CGroup: /system.slice/named.service
             └─4798 /usr/sbin/named -u named -c /etc/named.conf

Nov 17 10:10:11 dns named[4798]: zone 1.0.0.127.in-addr.arpa/IN: loaded serial 0
Nov 17 10:10:11 dns named[4798]: zone tp6.b1/IN: loaded serial 2019061800
Nov 17 10:10:11 dns named[4798]: zone 1.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.ip6.arpa/IN: loaded serial 0
Nov 17 10:10:11 dns named[4798]: zone localhost.localdomain/IN: loaded serial 0
Nov 17 10:10:11 dns named[4798]: zone localhost/IN: loaded serial 0
Nov 17 10:10:11 dns named[4798]: all zones loaded
Nov 17 10:10:11 dns systemd[1]: Started Berkeley Internet Name Domain (DNS).
Nov 17 10:10:11 dns named[4798]: running
Nov 17 10:10:12 dns named[4798]: managed-keys-zone: Initializing automatic trust anchor management for zone '.'; DNSKEY ID 20326 is now trusted, waiving th>
Nov 17 10:10:12 dns named[4798]: resolver priming query complete
```

- une commande ss qui prouve que le service écoute bien sur un port

```
[hugo@dns var]$ ss -atnl
State             Recv-Q            Send-Q                       Local Address:Port                         Peer Address:Port            Process
LISTEN            0                 10                               127.0.0.1:53                                0.0.0.0:*
LISTEN            0                 128                                0.0.0.0:22                                0.0.0.0:*
LISTEN            0                 10                              10.6.1.101:53                                0.0.0.0:*
LISTEN            0                 4096                             127.0.0.1:953                               0.0.0.0:*
LISTEN            0                 10                                   [::1]:53                                   [::]:*
LISTEN            0                 4096                                 [::1]:953                                  [::]:*
LISTEN            0                 128                                   [::]:22                                   [::]:*
```

🌞 Ouvrez le bon port dans le firewall

```
[hugo@dns var]$ sudo firewall-cmd --add-port=53/tcp --permanent
[sudo] password for hugo:
success
```

```
[hugo@dns var]$ sudo firewall-cmd --reload
success
```

### 3. Test

🌞 Sur la machine john.tp6.b1

- configurez la machine pour qu'elle utilise votre serveur DNS quand elle a besoin de résoudre des noms

```
[hugo@john ~]$ cat /etc/resolv.conf
# Generated by NetworkManager
nameserver 10.6.1.101
```

```
[hugo@john ~]$ dig dns.tp6.b1

; <<>> DiG 9.16.23-RH <<>> dns.tp6.b1
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 58710
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 10408e4412bf823f0100000065574b1a3ccef5b495becede (good)
;; QUESTION SECTION:
;dns.tp6.b1.                    IN      A

;; ANSWER SECTION:
dns.tp6.b1.             86400   IN      A       10.6.1.101

;; Query time: 3 msec
;; SERVER: 10.6.1.101#53(10.6.1.101)
;; WHEN: Fri Nov 17 12:14:33 CET 2023
;; MSG SIZE  rcvd: 83
```

```
[hugo@john ~]$ dig ynov.com

; <<>> DiG 9.16.23-RH <<>> ynov.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 59666
;; flags: qr rd ra; QUERY: 1, ANSWER: 3, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 768c8f4d9d43e9340100000065574ade9b86cee23b53542c (good)
;; QUESTION SECTION:
;ynov.com.                      IN      A

;; ANSWER SECTION:
ynov.com.               300     IN      A       104.26.10.233
ynov.com.               300     IN      A       104.26.11.233
ynov.com.               300     IN      A       172.67.74.226

;; Query time: 337 msec
;; SERVER: 10.6.1.101#53(10.6.1.101)
;; WHEN: Fri Nov 17 12:13:34 CET 2023
;; MSG SIZE  rcvd: 113
```

🌞 Sur votre PC

- utilisez une commande pour résoudre le nom john.tp6.b1 en utilisant 10.6.1.101 comme serveur DNS

```
PS C:\Users\gogob_jaotmdf> nslookup john.tp6.b1 10.6.1.101
Serveur :   UnKnown
Address:  10.6.1.101

Nom :    john.tp6.b1
Address:  10.6.1.11
```

#### dossier wireshark : tp6_dns.pcapng

## III. Serveur DHCP

🌞 Installer un serveur DHCP

```
#
# DHCP Server Configuration file.
#   see /usr/share/doc/dhcp-server/dhcpd.conf.example
#   see dhcpd.conf(5) man page
#
# create new
# specify domain name
option domain-name     "dns";
# specify DNS server's hostname or IP address
option domain-name-servers     10.6.1.101;
# default lease time
default-lease-time 600;
# max lease time
max-lease-time 7200;
# this DHCP server to be declared valid
authoritative;
# specify network address and subnetmask
subnet 10.6.1.0 netmask 255.255.255.0 {
    # specify the range of lease IP address
    range dynamic-bootp 10.6.1.13 10.6.1.37;
    # specify broadcast address
    option broadcast-address 10.6.1.255;
    # specify gateway
    option routers 10.6.1.254;
}
```

🌞 Test avec john.tp6.b1

- enlevez-lui son IP statique, et récupérez une IP en DHCP

```
DEVICE=enp0s3

BOOTPROTO=dhcp
ONBOOT=yes
```

- prouvez-que

- vous avez une IP dans la bonne range

```
[hugo@john ~]$ ip a
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:cf:ea:d2 brd ff:ff:ff:ff:ff:ff
    inet 10.6.1.13/24 brd 10.6.1.255 scope global dynamic noprefixroute enp0s3
       valid_lft 558sec preferred_lft 558sec
    inet6 fe80::a00:27ff:fecf:ead2/64 scope link
       valid_lft forever preferred_lft forever
```

- vous avez votre routeur configuré automatiquement comme passerelle

```
[hugo@john ~]$ ip route show
default via 10.6.1.254 dev enp0s3 proto static metric 100
default via 10.6.1.254 dev enp0s3 proto dhcp src 10.6.1.13 metric 100
10.6.1.0/24 dev enp0s3 proto kernel scope link src 10.6.1.13 metric 100
```

- vous avez votre serveur DNS automatiquement configuré

```
[hugo@john ~]$ cat /etc/resolv.conf
# Generated by NetworkManager
search dns
nameserver 10.6.1.101
```

- vous avez un accès internet

```
[hugo@john ~]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=115 time=16.1 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=115 time=16.1 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=115 time=15.7 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=115 time=17.2 ms
^C
--- 8.8.8.8 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3009ms
rtt min/avg/max/mdev = 15.688/16.272/17.216/0.569 ms
```

🌞 Requête web avec john.tp6.b1

- utilisez la commande curl pour faire une requête HTTP vers le site de votre choix

```
[hugo@john ~]$ curl ynov.com
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>302 Found</title>
</head><body>
<h1>Found</h1>
<p>The document has moved <a href="https://www.ynov.com/">here</a>.</p>
<hr>
<address>Apache/2.4.41 (Ubuntu) Server at ynov.com Port 80</address>
</body></html>
```

