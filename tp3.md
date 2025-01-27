🌞 **Script autoconfig.sh**

```
#!/bin/bash

log() {
    local level=$1
    local message=$2
    echo "$(date +"%H:%M:%S") [$level] $message"
}

if [[ "$(id -u)" -ne 0 ]]; then
    log "ERROR" "Ce script doit être exécuté en tant que root."
    exit 1
fi

log "INFO" "Le script d'autoconfiguration a démarré"
log "INFO" "Le script a bien été lancé en root"

selinux=$(getenforce)

if [[ "$selinux" == "Enforcing" ]]; then
    log "WARN" "SELinux est toujours activé"

    log "INFO" "Désactivation de SELinux temporaire avec setenforce 0)"
    setenforce 0

    log "INFO" "Désactivation de SELinux définitive"
    sed -i 's/enforcing/permissive/' /etc/selinux/config
fi

firewall=$(systemctl is-active firewalld)

if [[ "$firewall" == "active" ]]; then
    log "INFO" "Le firewall est activé"
else
    log "WARN" "Le firewall n'est pas activé"
fi

port_ssh=$(ss -tulnp | grep ':22' | wc -l)

if [[ "$port_ssh" != 0 ]]; then
    log "WARN" "Le service SSH tourne toujours sur le port 22/TCP"
else
    log "INFO" "ton service ssh ne tourne pas sur le port 22"
fi

nouveau_port=$RANDOM

if [[ "$port_ssh" != 0 ]]; then
    log "Modification du fichier de configuration SSH pour écouter sur le port $nouveau_port"
    sed -i "s/^Port 22/Port $nouveau_port/" /etc/ssh/sshd_config

    log "INFO" "Redémarrage du service SSH"
    systemctl restart sshd

    log "INFO" "Ouverture du port $nouveau_port/TCP dans firewalld"
    firewall-cmd --add-port=$nouveau_port/tcp --permanent &> /dev/null
    firewall-cmd --reload &> /dev/null
fi

hostname=$(hostnamectl | grep hostname | tr -s ' ' | cut -d' ' -f4)

if [[ "$hostname" != "music.tp3.b3" ]]; then 
    log "WARN" "La machine s'appelle toujours $hostname"
    hostnamectl set-hostname music.tp3.b3 
    log "INFO" "Changement du nom pour music.tp3.b3"
else
    log "INFO" "La machine s'appelle $hostname"
fi

if ! id -nG leobln | grep wheel &> /dev/null; then
    log "WARN" "L'utilisateur leobln n'est pas dans le groupe wheel !"
    log "INFO" "Ajout de l'utilisateur leobln au groupe wheel"
    usermod -aG wheel leobln
else 
    log "INFO" "leobln est dans le groupe wheel"
fi

log "INFO" "Le script d'autoconfiguration s'est correctement déroulé"
```
# Partie II : Serveur de streaming

🌞 **Exécution du script `autoconfig.sh` développé à la partie I**

```
[leobln@music autoconfig]$ sudo bash autoconfig.sh
11:31:17 [INFO] Le script d'autoconfiguration a démarré
11:31:17 [INFO] Le script a bien été lancé en root
11:31:17 [INFO] Le firewall est activé
11:31:17 [WARN] Le service SSH tourne toujours sur le port 22/TCP
11:31:17 [Modification du fichier de configuration SSH pour écouter sur le port 21621]
11:31:17 [INFO] Redémarrage du service SSH
11:31:17 [INFO] Ouverture du port 21621/TCP dans firewalld
11:31:19 [INFO] La machine s'appelle music.tp3.b3
11:31:19 [INFO] leobln est dans le groupe wheel
11:31:19 [INFO] Le script d'autoconfiguration s'est correctement déroulé
```

🌞 **Création d'un dossier où on hébergera les fichiers de musique**

```
[leobln@music ~]$ cd /srv
[leobln@music srv]$ sudo mkdir music
[leobln@music var]$ ls
adm  cache  crash  db  empty  ftp  games  kerberos  lib  local  lock  log  mail  music  nis  opt  preserve  run  spool  tmp  www

```

🌞 **Déposez quelques fichiers son là dedans**

```
PS C:\Users\leobln> scp -P 21621 'C:\Users\leobln\Music\Yehezkel Raz - Ballerina.mp3' leobln@10.3.1.11:/tmp
leobln@10.3.1.11's password:
Yehezkel Raz - Ballerina.mp3              100% 2807KB  44.9MB/s   00:00

[leobln@music tmp]$ sudo mv /tmp/Yehezkel\ Raz\ -\ Ballerina.mp3 /var/music/
[leobln@music tmp]$ cd /var/music/
[leobln@music music]$ ls
'Jamie Bathgate - Status.mp3'
'Jerusalem Symphony Orchestra - Flower Duet Lakmé.mp3'
'Semo - Slow Tide.mp3'
'Yehezkel Raz - Ballerina.mp3'

```

🌞 **Ajoutez les dépôts nécessaires pour installer Jellyfin**

```
[leobln@music ~]$ sudo dnf install --nogpgcheck https://mirrors.rpmfusion.org/nonfree/el/rpmfusion-nonfree-release-$(rpm -E %rhel).noarch.rpm -y
[leobln@music ~]$ sudo dnf config-manager --set-enabled crb
```

🌞 **Installer le paquet `jellyfin`**

```
[leobln@music ~]$ sudo dnf install -y jellyfin
```

🌞 **Lancer le service `jellyfin`**

```
[leobln@music ~]$ sudo systemctl start jellyfin
```

🌞 **Afficher la liste des ports TCP en écoute**

```
 sudo ss -tulnp | grep LISTEN | grep jellyfin
tcp   LISTEN 0      512          0.0.0.0:8096       0.0.0.0:*    users:(("jellyfin",pid=15891,fd=310))
```

🌞 **Ouvrir le port derrière lequel Jellyfin écoute**

```
[leobln@music ~]$ sudo firewall-cmd --add-port=8096/tcp --permanent
success
[leobln@music ~]$ sudo firewall-cmd --reload
success
```

🌞 **Visitez l'interface Web !**

```
[leobln@music ~]$  curl -I http://10.3.1.11:8096
HTTP/1.1 302 Found
Date: Wed, 15 Jan 2025 11:40:45 GMT
Server: Kestrel
Location: /web/index.html
```

# Partie III : Serveur de monitoring

🌞 **Dérouler le script `autoconfig.sh` développé à la partie I**

```
[leobln@music ~]$ scp /home/leobln/autoconfig/autoconfig.sh leobln@10.
3.1.12:/tmp/

[leobln@monitoring ~]$ cd /tmp/
[leobln@monitoring tmp]$ ls
autoconfig.sh

[leobln@monitoring tmp]$ sudo bash autoconfig.sh
[sudo] password for leobln:
12:56:49 [INFO] Le script d'autoconfiguration a démarré
12:56:49 [INFO] Le script a bien été lancé en root
12:56:49 [INFO] Le firewall est activé
12:56:49 [WARN] Le service SSH tourne toujours sur le port 22/TCP
12:56:49 [Modification du fichier de configuration SSH pour écouter sur le port 29986]
12:56:49 [INFO] Redémarrage du service SSH
12:56:49 [INFO] Ouverture du port 29986/TCP dans firewalld
12:56:51 [WARN] La machine s'appelle toujours monitoring.tp3.b1
12:56:51 [INFO] Changement du nom pour music.tp3.b3
12:56:51 [INFO] leobln est dans le groupe wheel
12:56:51 [INFO] Le script d'autoconfiguration s'est correctement déroulé
```

🌞 **Installer Netdata**

```
[leobln@music ~]$ sudo dnf install epel-release -y
[leobln@music ~]$ curl https://get.netdata.cloud/kickstart.sh > /tmp/netdata-kickstart.sh && sh /tmp/netdata-kickstart.sh --nightly-channel
```

🌞 **Ajouter un check TCP**

```
 sudo ss -tulnp | grep netdata
udp   UNCONN 0      0          127.0.0.1:8125       0.0.0.0:*    users:(("netdata",pid=22992,fd=59))
udp   UNCONN 0      0              [::1]:8125          [::]:*    users:(("netdata",pid=22992,fd=58))
tcp   LISTEN 0      4096         0.0.0.0:19999      0.0.0.0:*    users:(("netdata",pid=22992,fd=6))
tcp   LISTEN 0      4096       127.0.0.1:8125       0.0.0.0:*    users:(("netdata",pid=22992,fd=61))
tcp   LISTEN 0      4096            [::]:19999         [::]:*    users:(("netdata",pid=22992,fd=7))
tcp   LISTEN 0      4096           [::1]:8125          [::]:*    users:(("netdata",pid=22992,fd=60))
[leobln@music netdata]$ sudo firewall-cmd --add-port=19999/tcp --permanent
success
[leobln@music netdata]$ sudo firewall-cmd --reload
success
```

🌞 **Ajout d'une alerte Discord**

```

```