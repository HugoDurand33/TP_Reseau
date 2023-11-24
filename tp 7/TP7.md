# TP7 : Do u secure

## I. Setup LAN

## II. SSH

### 1. Fingerprint

🌞 Effectuez une connexion SSH en vérifiant le fingerprint

- en rendu je veux voir le message du serveur à la première connexion

```
PS C:\Users\gogob_jaotmdf> ssh hugo@10.7.1.11
The authenticity of host '10.7.1.11 (10.7.1.11)' can't be established.
ED25519 key fingerprint is SHA256:1J9viCbY53SOJv7GF9nA0BgcjMyvJps4F/3iVHo5cKg.
This host key is known by the following other names/addresses:
    C:\Users\gogob_jaotmdf/.ssh/known_hosts:4: 10.3.1.12
    C:\Users\gogob_jaotmdf/.ssh/known_hosts:7: 10.3.1.11
    C:\Users\gogob_jaotmdf/.ssh/known_hosts:8: 10.4.1.12
    C:\Users\gogob_jaotmdf/.ssh/known_hosts:9: 10.4.1.253
    C:\Users\gogob_jaotmdf/.ssh/known_hosts:10: 10.4.1.254
    C:\Users\gogob_jaotmdf/.ssh/known_hosts:11: 10.5.1.11
    C:\Users\gogob_jaotmdf/.ssh/known_hosts:12: 10.5.1.254
    C:\Users\gogob_jaotmdf/.ssh/known_hosts:13: 10.5.1.12
    (6 additional names omitted)
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

- et je veux aussi une commande qui me montre l'empreinte du serveur

```
[hugo@john ~]$ sudo ssh-keygen -l -f /etc/ssh/ssh_host_ed25519_key
[sudo] password for hugo:
256 SHA256:1J9viCbY53SOJv7GF9nA0BgcjMyvJps4F/3iVHo5cKg /etc/ssh/ssh_host_ed25519_key.pub (ED25519)
```
### 2. Conf serveur SSH

🌞 Consulter l'état actuel

- vérifiez que le serveur SSH tourne actuellement sur le port 22/tcp

```
[hugo@john ~]$ ss -atnl
State         Recv-Q        Send-Q               Local Address:Port               Peer Address:Port       Process
LISTEN        0             128                        0.0.0.0:22                      0.0.0.0:*
LISTEN        0             128                           [::]:22                         [::]:*
```

🌞 Modifier la configuration du serveur SSH

```
[hugo@router ~]$ sudo nano /etc/ssh/sshd_config
```

```
Port 25555
#AddressFamily any
ListenAddress 10.7.1.254
#ListenAddress ::
```

```
[hugo@router ~]$ sudo systemctl restart sshd
```

🌞 Prouvez que le changement a pris effet

- éditer le fichier de configuration du serveur SSH pour spécifier que 

```
[hugo@router ~]$ ss -atnl
State        Recv-Q        Send-Q               Local Address:Port                Peer Address:Port       Process
LISTEN       0             128                     10.7.1.254:25555                    0.0.0.0:*
```

🌞 N'oubliez pas d'ouvrir ce nouveau port dans le firewall

```
[hugo@router ~]$ sudo firewall-cmd --add-port=25555/tcp --permanent
```

```
[hugo@router ~]$ sudo firewall-cmd --reload
```

🌞 Effectuer une connexion SSH sur le nouveau port

```
PS C:\Users\gogob_jaotmdf> ssh hugo@10.7.1.254 -p 25555
Last login: Fri Nov 24 09:15:22 2023
[hugo@router ~]$
```

### 3. Connexion par clé

🌞 Générer une paire de clés

- j'ai déjà une paire de clé.

🌞 Déposer la clé publique sur une VM

- déjà fait dans le clone originel.

🌞 Connectez-vous en SSH à la machine

- connecez-vous donc à john

```
PS C:\Users\gogob_jaotmdf> ssh hugo@10.7.1.11
Last login: Fri Nov 24 09:16:31 2023 from 10.7.1.1
[hugo@john ~]$
```

🌞 Supprimer les clés sur la machine router.tp7.b1

```
[hugo@router ssh]$ sudo rm ssh_host_ecdsa_key.pub
[sudo] password for hugo:
[hugo@router ssh]$ sudo rm ssh_host_ed25519_key
[hugo@router ssh]$ sudo rm ssh_host_ed25519_key.pub
[hugo@router ssh]$ sudo rm ssh_host_rsa_key
[hugo@router ssh]$ sudo rm ssh_host_rsa_key.pub
```

🌞 Regénérez les clés sur la machine router.tp7.b1

```
[hugo@router ssh]$ sudo ssh-keygen -A
ssh-keygen: generating new host keys: RSA DSA ECDSA ED25519
```

🌞 Tentez une nouvelle connexion au serveur

```
PS C:\Users\gogob_jaotmdf> ssh hugo@10.7.1.254 -p 25555
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ED25519 key sent by the remote host is
SHA256:1bnwpHEejI7VlJTg15lhK2fq8RIaINKlwMb8C5ksy1Q.
Please contact your system administrator.
Add correct host key in C:\\Users\\gogob_jaotmdf/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in C:\\Users\\gogob_jaotmdf/.ssh/known_hosts:24
Host key for [10.7.1.254]:25555 has changed and you have requested strict checking.
Host key verification failed.
PS C:\Users\gogob_jaotmdf>
```

## III. Web sécurisé

### 0. Setup

🌞 Montrer sur quel port est disponible le serveur web

```
[hugo@web conf.d]$ ss -atnl
State   Recv-Q  Send-Q   Local Address:Port     Peer Address:Port  Process
LISTEN  0       511            0.0.0.0:80            0.0.0.0:*
LISTEN  0       128            0.0.0.0:22            0.0.0.0:*
LISTEN  0       511               [::]:80               [::]:*
LISTEN  0       128               [::]:22               [::]:*
```
### 1. Setup HTTPS

🌞 Générer une clé et un certificat sur web.tp7.b1

```
[hugo@web conf.d]$ sudo openssl req -new -newkey rsa:2048 -days 365 -nodes -x509 -keyout server.key -out server.crt
[sudo] password for hugo:
.+.......+.....+....+......+........+...+....+......+..+.............+..+...............+..........+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*....+......+..+...+...+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.+...+..........+.....+....+...+...+..................+..............+.+.........+...+...+..+....+........+.+...........+.+........+....+..+...+...+...+......+.+...+.........+...+..+...+.........+.........+.+.....................+..+.......+........+.......+......+.........+......+.....+...+....+..+....+...........................+...+.....+......+....+......+.....+....+..+............+......+.+.....+....+..+.......+...+..+...+..................+....+...+.....+...+.......+.........+......+.....+.........+..........+...+........+..........+..+...............+.+..+.............+....................+......+.+.....+............+............+.+........+..........+.........+..+.........+..........+.....+.+...+...+..+.......+........+...+....+...+..+..........+...+......+...........+...+...+...............+....+...........+....+...+..+.+..+......+............+...............+...+....+..+.............+........+....+........+.+......+......+........+......+...+.+...........+....+...........+.+..+......+............+...+......+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
........+......+.......................+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*...+...+...+...+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*....+........+.+......+........+.......+.....+...+......+......+.............+......+..+..........+.....+....+...+......+..+...+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:fr
State or Province Name (full name) []:gironde
Locality Name (eg, city) [Default City]:bordeaux
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server's hostname) []:web
Email Address []:.
```

```
[hugo@web conf.d]$ sudo mv server.key /etc/pki/tls/private/web.tp7.b1.key
[hugo@web conf.d]$ sudo mv server.crt /etc/pki/tls/certs/web.tp7.b1.crt
[hugo@web conf.d]$ sudo chown nginx:nginx /etc/pki/tls/private/web.tp7.b1.key
[hugo@web conf.d]$ sudo chown nginx:nginx /etc/pki/tls/certs/web.tp7.b1.crt
[hugo@web conf.d]$ sudo chmod 0400 /etc/pki/tls/private/web.tp7.b1.key
[hugo@web conf.d]$ sudo chmod 0444 /etc/pki/tls/certs/web.tp7.b1.crt
```

🌞 Modification de la conf de NGINX

```
[hugo@web conf.d]$ sudo cat /etc/nginx/conf.d/site_web_nul.conf
server {
  # le port sur lequel on veut écouter
  listen 10.7.1.12:443 ssl;

  ssl_certificate /etc/pki/tls/certs/web.tp7.b1.crt;
  ssl_certificate_key /etc/pki/tls/private/web.tp7.b1.key;
  # le nom de la page d'accueil si le client de la précise pas
  index index.html;

  # un nom pour notre serveur (pas vraiment utile ici, mais bonne pratique)
  server_name www.site_web_nul.b1;

  # le dossier qui contient notre site web
  root /var/www/site_web_nul;
}
```

🌞 Conf firewall

```
[hugo@web conf.d]$ sudo firewall-cmd --add-port=443/tcp --permanent
success
[hugo@web conf.d]$ sudo firewall-cmd --reload
success
```

🌞 Redémarrez NGINX

```
[hugo@web conf.d]$ sudo systemctl restart nginx
```

🌞 Prouvez que NGINX écoute sur le port 443/tcp

```
[hugo@web conf.d]$ ss -atnl
State   Recv-Q  Send-Q   Local Address:Port     Peer Address:Port  Process
LISTEN  0       511          10.7.1.12:443           0.0.0.0:*
```

🌞 Visitez le site web en https

```
[hugo@john ~]$ curl -k https://10.7.1.12
<h1>Ceci n'est pas un site web</h1>

<h2>Le message en haut ment</h2>
```
