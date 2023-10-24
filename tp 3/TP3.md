# tp 3

## I. ARP

### 1. Echange ARP

🌞Générer des requêtes ARP

```ping 10.3.1.11```

```ip neigh show```

arp machine john :

10.3.1.12 dev enp0s3 lladdr ⭐08:00:27:53:86:9e⭐ STALE

10.3.1.1 dev enp0s3 lladdr 0a:00:27:00:00:28 DELAY

arp machine marcel :

10.3.1.1 dev enp0s3 lladdr 0a:00:27:00:00:28 REACHABLE

10.3.1.11 dev enp0s3 lladdr ⭐08:00:27:c8:23:35⭐ REACHABLE

```ip neigh show 10.3.1.11```

10.3.1.11 dev enp0s3 lladdr 08:00:27:c8:23:35 STALE

```ip a```



