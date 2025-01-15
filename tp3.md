🌞 Script autoconfig.sh

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