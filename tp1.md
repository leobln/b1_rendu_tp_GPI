# I. Service SSH

🌞 **S'assurer que le service `sshd` est démarré**

```
[leobln@web ~]$ systemctl status sshd
● sshd.service - OpenSSH server daemon
     Loaded: loaded (/usr/lib/systemd/system/sshd.service; enabled; preset>
     Active: active (running) since Mon 2024-12-02 15:34:25 CET; 25min ago
       Docs: man:sshd(8)
             man:sshd_config(5)
   Main PID: 1537 (sshd)
      Tasks: 1 (limit: 11083)
     Memory: 3.4M
        CPU: 213ms
     CGroup: /system.slice/sshd.service
             └─1537 "sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startu>
Dec 02 15:34:25 web.tp1.b1 systemd[1]: Starting OpenSSH server daemon...
Dec 02 15:34:25 web.tp1.b1 sshd[1537]: Server listening on 0.0.0.0 port 22.
Dec 02 15:34:25 web.tp1.b1 sshd[1537]: Server listening on :: port 22.
Dec 02 15:34:25 web.tp1.b1 systemd[1]: Started OpenSSH server daemon.
Dec 02 15:35:10 web.tp1.b1 sshd[1551]: Accepted password for leobln from 1>
Dec 02 15:35:10 web.tp1.b1 sshd[1551]: pam_unix(sshd:session): session ope>
Dec 02 15:42:31 web.tp1.b1 sshd[1587]: Accepted password for leobln from 1>
Dec 02 15:42:31 web.tp1.b1 sshd[1587]: pam_unix(sshd:session): session ope>
Dec 02 15:57:54 web.tp1.b1 sshd[1655]: Accepted password for leobln from 1>
Dec 02 15:57:54 web.tp1.b1 sshd[1655]: pam_unix(sshd:session): session ope>
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
# II. Service HTTP

🌞 **Installer le serveur NGINX**

```
[leobln@web ~]$ sudo dnf install nginx
```

🌞 **Démarrer le service NGINX**

```
[leobln@web ~]$ sudo systemctl start nginx
[leobln@web ~]$ sudo systemctl status nginx
● nginx.service - The nginx HTTP and reverse proxy server
     Loaded: loaded (/usr/lib/systemd/system/nginx.service; enabled; prese>
     Active: active (running)
```

🌞 **Déterminer sur quel port tourne NGINX**

```
[leobln@web ~]$ grep listen /etc/nginx/nginx.conf
        listen       80;
        listen       [::]:80;
[leobln@web ~]$ sudo firewall-cmd --permanent --add-port=80/tcp
success
[leobln@web ~]$ sudo firewall-cmd --reload
success
[leobln@web ~]$ sudo firewall-cmd --list-all | grep ports
  ports: 22/tcp 80/tcp 443/tcp
  forward-ports:
  source-ports:
```

🌞 **Déterminer les processus liés au service NGINX**

```
[leobln@web ~]$ ps aux | grep nginx
root        1878  0.0  0.0  11292  1464 ?        Ss   16:24   0:00 nginx: master process /usr/sbin/nginx
nginx       1879  0.0  0.2  15532  4920 ?        S    16:24   0:00 nginx: worker process
leobln      1957  0.0  0.1   6408  2176 pts/0    S+   16:49   0:00 grep --color=auto nginx
```

🌞 **Déterminer le nom de l'utilisateur qui lance NGINX**

```
[leobln@web ~]$ cat /etc/passwd | grep leobln
leobln:x:1000:1000:leobln:/home/leobln:/bin/bash
```

🌞 **Test !**

```
[leobln@web ~]$ curl http://10.1.1.1:80 | head -n 7
```

## 2. Analyser la conf de NGINX

🌞 **Déterminer le path du fichier de configuration de NGINX**

```
[leobln@web ~]$ ls -al /etc/nginx/nginx.conf
-rw-r--r--. 1 root root 2334 Nov  8 17:43 /etc/nginx/nginx.conf
```

🌞 **Trouver dans le fichier de conf**

```
[leobln@web ~]$ cat /etc/nginx/nginx.conf | grep "server" -A 10
[leobln@web ~]$ cat /etc/nginx/nginx.conf | grep "include"
[leobln@web ~]$ cat /etc/nginx/nginx.conf | grep "user"
```

## 3. Déployer un nouveau site web

🌞 **Créer un site web**

```
[leobln@web ~]$ pwd
/home/leobln
[leobln@web ~]$ sudo mkdir /home/leobln/var
[leobln@web ~]$ cd var
[leobln@web var]$ sudo mkdir /home/leobln/var/www
[leobln@web var]$ cd www
[leobln@web www]$ sudo mkdir /home/leobln/var/www/tp1_parc
[leobln@web www]$ cd tp1_parc
[leobln@web tp1_parc]$ sudo nano index.html
```

🌞 **Gérer les permissions**

```
[leobln@web tp1_parc]$ sudo chown -R nginx:nginx /home/leobln/var/www/tp1_
parc
[leobln@web tp1_parc]$ ls -al /home/leobln/var/www/tp1_parc
total 4
drwxr-xr-x. 2 nginx nginx 24 Dec  2 17:35 .
drwxr-xr-x. 3 root  root  22 Dec  2 17:21 ..
-rw-r--r--. 1 nginx nginx 38 Dec  2 17:35 index.html
```

🌞 **Adapter la conf NGINX**

- dans le fichier de conf principal
  - vous supprimerez le bloc `server {}` repéré plus tôt pour que NGINX ne serve plus le site par défaut (parce que ça sert à rien le site par défaut)
  - redémarrez NGINX pour que les changements prennent effet
- créez un nouveau fichier de conf
  - il doit être nommé correctement
  - il doit être placé dans le bon dossier
  - c'est quoi un "nom correct" et "le bon dossier" ?
    - bah vous avez repéré dans la partie d'avant les fichiers qui sont inclus par le fichier de conf principal non ?
    - créez votre fichier en conséquence
  - redémarrez NGINX pour que les changements prennent effet
  - le contenu doit être le suivant :
    - il écoute sur un port que vous aurez déterminé aléatoirement avec `echo $RANDOM`
      - n'oubliez pas d'ouvrir ce port dans le firewall, et fermer l'ancien
    - il définit que le site web est stocké dans /var/www/tp1_parc

```nginx
server {
  # le port choisi devra être obtenu avec un 'echo $RANDOM' là encore
  listen <PORT>;

  root /var/www/tp1_parc;
}
```

🌞 **Visitez votre super site web**

- toujours avec une commande `curl` depuis votre PC (ou un navigateur)


