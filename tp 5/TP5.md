# tp 5

## I. First steps

🌞 Déterminez, pour ces 5 applications, si c'est du TCP ou de l'UDP

- discord : (udp)

    ip : 66.22.197.97
    port serveur : port 50005
    port local : port 62809

#### dossier wireshark : tp5_service_1.pcapng


- projet-voltaire : (tcp)

    ip : 104.22.5.149
    port serveur : port 443
    port local : port 50502

#### dossier wireshark : tp5_service_2.pcapng

- teams : (udp)

    ip : 52.112.175.233
    port serveur : 3478
    port local : 50001

#### dossier wireshark : tp5_service_3.pcapng

- gouvernement.fr : (tcp)

    ip : 192.229.221.103
    port serveur : 443
    port local : 51075

#### dossier wireshark : tp5_service_4.pcapng

- home.nra.org : (tcp)

    ip : 104.18.36.195
    port serveur : 443
    port local : 51124

#### dossier wireshark : tp5_service_5.pcapng

🌞 Demandez l'avis à votre OS

```
PS C:\Users\gogob_jaotmdf> netstat

Connexions actives

  Proto  Adresse locale         Adresse distante       État

  # discord
  UDP    10.33.70.99:62809     66.22.197.97:50005    ESTABLISHED

  # site projet-voltaire
  TCP    10.33.70.99:50502     104.22.5.149:https  ESTABLISHED

  # appli teams
  UDP    10.33.70.99:50001     52.112.175.233:3478     ESTABLISHED

  # site gouvernement.fr
  TCP    10.33.70.99:51075     192.229.221.103:https        ESTABLISHED
  
  # site nra
  TCP    10.33.70.999:51124     104.18.36.195:https    ESTABLISHED
```

## II. Setup Virtuel

### 1. SSH

🌞 Examinez le trafic dans Wireshark

- déterminez si SSH utilise TCP ou UDP

    le ssh utilise TCP, si il utilise UDP il ne serai pas fiable

🌞 Demandez aux OS

- repérez, avec une commande adaptée (netstat ou ss), la connexion SSH depuis votre machine

```
PS C:\Users\gogob_jaotmdf> netstat

Connexions actives

  Proto  Adresse locale         Adresse distante       État
  TCP    10.5.1.1:50748         10.5.1.11:ssh           ESTABLISHED
```

- ET repérez la connexion SSH depuis la VM

```
[hugo@node1 ~]$ ss
Netid State Recv-Q Send-Q                Local Address:Port  Peer Address:Port  Process

tcp   ESTAB 0      0                         10.5.1.11:ssh       10.5.1.1:50858

```

#### dossier wireshark : tp5_3_way.pcapng

### 2. Routage

🌞 Prouvez que

- node1.tp5.b1 a un accès internet

```
[hugo@node1 ~]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=112 time=22.9 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=112 time=22.2 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=112 time=18.6 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=112 time=25.0 ms
64 bytes from 8.8.8.8: icmp_seq=5 ttl=112 time=24.1 ms
^C
--- 8.8.8.8 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4011ms
rtt min/avg/max/mdev = 18.568/22.546/24.975/2.203 ms
``` 

- node1.tp5.b1 peut résoudre des noms de domaine publics (comme www.ynov.com)

```
[hugo@node1 ~]$ ping ynov.com
PING ynov.com (104.26.10.233) 56(84) bytes of data.
64 bytes from 104.26.10.233 (104.26.10.233): icmp_seq=1 ttl=55 time=250 ms
64 bytes from 104.26.10.233 (104.26.10.233): icmp_seq=2 ttl=55 time=24.1 ms
64 bytes from 104.26.10.233 (104.26.10.233): icmp_seq=3 ttl=55 time=28.3 ms
64 bytes from 104.26.10.233 (104.26.10.233): icmp_seq=4 ttl=55 time=25.4 ms
^C64 bytes from 104.26.10.233: icmp_seq=5 ttl=55 time=26.1 ms

--- ynov.com ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 20528ms
rtt min/avg/max/mdev = 24.079/70.866/250.413/89.783 ms
```

### 3. Serveur Web

🌞 Installez le paquet nginx

```
[hugo@web ~]$ sudo dnf install nginx
```

🌞 Créer le site web

```
[hugo@web var]$ mkdir www
```

```
[hugo@web www]$ sudo mkdir site_web_nul
```

```
[hugo@web site_web_nul]$ cat index.html
<h1>MEOW meow</h1>
```

🌞 Donner les bonnes permissions

```
[hugo@web site_web_nul]$ sudo chown -R nginx:nginx /var/www/site_web_nul
```

🌞 Créer un fichier de configuration NGINX pour notre site web

```
[hugo@web conf.d]$ sudo nano site_web_nul.conf
```

🌞 Démarrer le serveur web !

```
[hugo@web conf.d]$ sudo systemctl start nginx
```

```
[hugo@web conf.d]$ systemctl status nginx
● nginx.service - The nginx HTTP and reverse proxy server
     Loaded: loaded (/usr/lib/systemd/system/nginx.service; disabled; prese>     Active: active (running) since Fri 2023-11-10 12:08:43 CET; 10s ago
    Process: 1547 ExecStartPre=/usr/bin/rm -f /run/nginx.pid (code=exited, >    Process: 1548 ExecStartPre=/usr/sbin/nginx -t (code=exited, status=0/SU>    Process: 1549 ExecStart=/usr/sbin/nginx (code=exited, status=0/SUCCESS)
   Main PID: 1550 (nginx)
      Tasks: 2 (limit: 4674)
     Memory: 2.0M
        CPU: 53ms
     CGroup: /system.slice/nginx.service
             ├─1550 "nginx: master process /usr/sbin/nginx"
             └─1551 "nginx: worker process"

Nov 10 12:08:43 web systemd[1]: Starting The nginx HTTP and reverse proxy s>Nov 10 12:08:43 web nginx[1548]: nginx: the configuration file /etc/nginx/n>Nov 10 12:08:43 web nginx[1548]: nginx: configuration file /etc/nginx/nginx>Nov 10 12:08:43 web systemd[1]: Started The nginx HTTP and reverse proxy se>lines 1-18/18 (END)...skipping...
● nginx.service - The nginx HTTP and reverse proxy server
     Loaded: loaded (/usr/lib/systemd/system/nginx.service; disabled; preset: disabled)
     Active: active (running) since Fri 2023-11-10 12:08:43 CET; 10s ago
    Process: 1547 ExecStartPre=/usr/bin/rm -f /run/nginx.pid (code=exited, status=0/SUCCESS)
    Process: 1548 ExecStartPre=/usr/sbin/nginx -t (code=exited, status=0/SUCCESS)
    Process: 1549 ExecStart=/usr/sbin/nginx (code=exited, status=0/SUCCESS)
   Main PID: 1550 (nginx)
      Tasks: 2 (limit: 4674)
     Memory: 2.0M
        CPU: 53ms
     CGroup: /system.slice/nginx.service
             ├─1550 "nginx: master process /usr/sbin/nginx"
             └─1551 "nginx: worker process"

Nov 10 12:08:43 web systemd[1]: Starting The nginx HTTP and reverse proxy server...
Nov 10 12:08:43 web nginx[1548]: nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
Nov 10 12:08:43 web nginx[1548]: nginx: configuration file /etc/nginx/nginx.conf test is successful
Nov 10 12:08:43 web systemd[1]: Started The nginx HTTP and reverse proxy server.
```

🌞 Ouvrir le port firewall

```
[hugo@web conf.d]$ sudo firewall-cmd --add-port=80/tcp --permanent
```

```
[hugo@web conf.d]$ sudo firewall-cmd --reload
```

🌞 Visitez le serveur web !

```
[hugo@node1 ~]$ curl 10.5.1.12
```

🌞 Visualiser le port en écoute

- avec une commande ss dans le terminal depuis web.tp5.b1

```
[hugo@web ~]$ ss -atnl
State    Recv-Q   Send-Q     Local Address:Port     Peer Address:Port  Process
LISTEN   0        511              0.0.0.0:80            0.0.0.0:*
LISTEN   0        128              0.0.0.0:22            0.0.0.0:*
LISTEN   0        511                 [::]:80               [::]:*
LISTEN   0        128                 [::]:22               [::]:*

```

🌞 Analyse trafic

 #### dossier wireshark : tp5_web.pcapng