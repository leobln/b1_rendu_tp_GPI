# I. Service SSH

🌞 **S'assurer que le service `sshd` est démarré**

```
[leobln@web ~]$ systemctl status
● web.tp1.b1
    State: running
```

🌞 **Analyser les processus liés au service SSH**



🌞 **Déterminer le port sur lequel écoute le service SSH**

- avec une commande `ss`
  - il faudra ajouter des options à la commandes `ss` pour que ce soit des infos plus lisibles (encore une fois, [voir le mémoooooo y'a des exemples de commandes](../../cours/memo/shell.md).
- isolez les lignes intéressantes avec un `| grep <TEXTE>`

🌞 **Consulter les logs du service SSH**

- les logs du service sont consultables avec une commande `journalctl`
  - donnez une commande `journalctl` qui permet de consulter les logs du service SSH
- AUSSI, il existe un fichier de log, dans lequel le service SSH enregistre toutes les tentatives de connexion
  - il est dans le dossier `/var/log`
  - utilisez une commande `tail` pour visualiser les 10 dernière lignes de ce fichier

![When she tells you](./img/when_she_tells_you.png)

## 2. Modification du service

Dans cette section, on va aller visiter et modifier le fichier de configuration du serveur SSH.

Comme tout fichier de configuration, celui de SSH se trouve dans le dossier `/etc/`.

Plus précisément, il existe un sous-dossier `/etc/ssh/` qui contient toute la configuration relative à SSH spécifiquement

🌞 **Identifier le fichier de configuration du serveur SSH**

```
[leobln@web ssh]$ ls -l sshd_config
-rw-------. 1 root root 3667 Nov  5 04:37 sshd_config
```

🌞 **Modifier le fichier de conf**

```
[leobln@web ~]$ echo $RANDOM
12899
[leobln@web ~]$ sudo nano /etc/ssh/sshd_config
port 12899
[leobln@web ~]$ sudo cat /etc/ssh/sshd_config | grep Port
Port 12899
#GatewayPorts no
[leobln@web ~]$ sudo firewall-cmd --permanent --remove-port=22/tcp
[sudo] password for leobln:
Warning: NOT_ENABLED: 22:tcp
success
[leobln@web ~]$ sudo firewall-cmd --reload
success
[leobln@web ~]$ sudo firewall-cmd --permanent --add-port=12899/tcp
success
[leobln@web ~]$ sudo firewall-cmd --list-all | grep port
  ports: 12899/tcp
  forward-ports:
  source-ports:
```

🌞 **Redémarrer le service**

```
[leobln@web ~]$ sudo systemctl status sshd
● sshd.service - OpenSSH server daemon
     Loaded: loaded (/usr/lib/systemd/system/sshd.service; enabled; preset:>
     Active: active (running) since Mon 2024-12-02 15:20:08 CET; 31s ago
       Docs: man:sshd(8)
             man:sshd_config(5)
   Main PID: 1486 (sshd)
      Tasks: 1 (limit: 11083)
     Memory: 1.4M
        CPU: 22ms
     CGroup: /system.slice/sshd.service
             └─1486 "sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startup>

Dec 02 15:20:08 web.tp1.b1 systemd[1]: Starting OpenSSH server daemon...
Dec 02 15:20:08 web.tp1.b1 sshd[1486]: Server listening on 0.0.0.0 port 128>
Dec 02 15:20:08 web.tp1.b1 sshd[1486]: Server listening on :: port 12899.
Dec 02 15:20:08 web.tp1.b1 systemd[1]: Started OpenSSH server daemon.
```

🌞 **Effectuer une connexion SSH sur le nouveau port**

```
[leobln@web ~]$ exit
logout
Connection to 10.1.1.1 closed.
PS C:\Users\leobln> ssh -p 12899 leobln@10.1.1.1
leobln@10.1.1.1's password:
Last login: Mon Dec  2 14:59:32 2024 from 10.1.1.3
[leobln@web ~]$

[leobln@web ~]$ sudo nano /etc/ssh/sshd_config
[sudo] password for leobln:
[leobln@web ~]$ sudo systemctl restart sshd
[leobln@web ~]$ sudo firewall-cmd --permanent --remove-port=12899/tcp
success
[leobln@web ~]$ sudo firewall-cmd --permanent --add-port=22/tcp
success
[leobln@web ~]$ sudo firewall-cmd --reload
success
[leobln@web ~]$ exit
logout
Connection to 10.1.1.1 closed.
PS C:\Users\leobln> ssh leobln@10.1.1.1
leobln@10.1.1.1's password:
Last login: Mon Dec  2 15:29:22 2024 from 10.1.1.3
```

