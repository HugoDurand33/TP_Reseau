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