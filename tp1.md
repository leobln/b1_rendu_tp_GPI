# I. Service SSH

🌞 **S'assurer que le service `sshd` est démarré**

```
[leobln@web ~]$ systemctl status
● web.tp1.b1
    State: running
```

🌞 **Analyser les processus liés au service SSH**

```
[leobln@web ~]$ ps aux | grep sshd
root        1537  0.0  0.5  16800  9216 ?        Ss   15:34   0:00 sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups
root        1587  0.0  0.6  20156 11392 ?        Ss   15:42   0:00 sshd: leobln [priv]
leobln      1591  0.0  0.3  20352  6796 ?        S    15:42   0:00 sshd: leobln@pts/0
leobln      1620  0.0  0.1   6408  2304 pts/0    S+   15:44   0:00 grep --color=auto sshd
```

🌞 **Déterminer le port sur lequel écoute le service SSH**

```
[leobln@web ~]$ ss -tuln | grep 22
tcp   LISTEN 0      128          0.0.0.0:22        0.0.0.0:*
tcp   LISTEN 0      128             [::]:22           [::]:*
```

🌞 **Consulter les logs du service SSH**

```
[leobln@web ~]$ sudo journalctl -u sshd -n 10
Dec 02 15:34:25 web.tp1.b1 systemd[1]: sshd.service: Deactivated successfu>
Dec 02 15:34:25 web.tp1.b1 systemd[1]: Stopped OpenSSH server daemon.
Dec 02 15:34:25 web.tp1.b1 systemd[1]: Starting OpenSSH server daemon...
Dec 02 15:34:25 web.tp1.b1 sshd[1537]: Server listening on 0.0.0.0 port 22.
Dec 02 15:34:25 web.tp1.b1 sshd[1537]: Server listening on :: port 22.
Dec 02 15:34:25 web.tp1.b1 systemd[1]: Started OpenSSH server daemon.
Dec 02 15:35:10 web.tp1.b1 sshd[1551]: Accepted password for leobln from 1>
Dec 02 15:35:10 web.tp1.b1 sshd[1551]: pam_unix(sshd:session): session ope>
Dec 02 15:42:31 web.tp1.b1 sshd[1587]: Accepted password for leobln from 1>
Dec 02 15:42:31 web.tp1.b1 sshd[1587]: pam_unix(sshd:session): session ope>
[leobln@web ~]$ sudo tail -n 10 /var/log/auth.log
tail: cannot open '/var/log/auth.log' for reading: No such file or directory
[leobln@web ~]$ sudo tail -n 10 /var/log/secure
Dec  2 15:50:37 web sudo[1630]: pam_unix(sudo:session): session opened for user root(uid=0) by leobln(uid=1000)
Dec  2 15:50:44 web sudo[1630]: pam_unix(sudo:session): session closed for user root
Dec  2 15:51:22 web sudo[1636]:  leobln : TTY=pts/0 ; PWD=/home/leobln ; USER=root ; COMMAND=/bin/journalctl -u sshd -n 10
Dec  2 15:51:22 web sudo[1636]: pam_unix(sudo:session): session opened for user root(uid=0) by leobln(uid=1000)
Dec  2 15:51:26 web sudo[1636]: pam_unix(sudo:session): session closed for user root
Dec  2 15:52:10 web sudo[1640]:  leobln : TTY=pts/0 ; PWD=/home/leobln ; USER=root ; COMMAND=/bin/tail -n 10 /var/log/auth.log
Dec  2 15:52:10 web sudo[1640]: pam_unix(sudo:session): session opened for user root(uid=0) by leobln(uid=1000)
Dec  2 15:52:10 web sudo[1640]: pam_unix(sudo:session): session closed for user root
Dec  2 15:52:22 web sudo[1643]:  leobln : TTY=pts/0 ; PWD=/home/leobln ; USER=root ; COMMAND=/bin/tail -n 10 /var/log/secure
Dec  2 15:52:22 web sudo[1643]: pam_unix(sudo:session): session opened for user root(uid=0) by leobln(uid=1000)
```

## 2. Modification du service

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

