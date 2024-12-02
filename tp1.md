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

- avec une commande `systemctl restart`

🌞 **Effectuer une connexion SSH sur le nouveau port**

- depuis votre PC
- il faudra utiliser une option à la commande `ssh` pour vous connecter à la VM

> Je vous conseille de remettre le port par défaut une fois que cette partie est terminée.

✨ **Bonus : affiner la conf du serveur SSH**

- faites vos plus belles recherches internet pour améliorer la conf de SSH
- par "améliorer" on entend essentiellement ici : augmenter son niveau de sécurité
- le but c'est pas de me rendre 10000 lignes de conf que vous pompez sur internet pour le bonus, mais de vous éveiller à divers aspects de SSH, la sécu ou d'autres choses liées

![Such a hacker](./img/such_a_hacker.png)
aa