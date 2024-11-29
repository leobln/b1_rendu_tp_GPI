# I. Service SSH

🌞 **S'assurer que le service `sshd` est démarré**

```
[leobln@web ~]$ systemctl status
● web.tp1.b1
    State: running aeaer
```

🌞 **Analyser les processus liés au service SSH**

- afficher les processus liés au service `sshd`
  - vous pouvez afficher la liste des processus en cours d'exécution avec une commande `ps`
  - pour rappel "un processus" c'est juste un programme qu'on a "lancé", qui a donc été déplacé en RAM et qui est en cours d'exécution
  - pour le compte-rendu, vous devez filtrer la sortie de la commande en ajoutant `| grep <TEXTE_RECHERCHE>` après une commande
    - au cas où un ptit rappel de l'utilisation de `| grep` :

```bash
# Exemple de manipulation de | grep

# admettons un fichier texte appelé "fichier_demo"
# on peut afficher son contenu avec la commande cat :
$ cat fichier_demo
bob a un chapeau rouge
emma surfe avec un dinosaure
eve a pas toute sa tête

# il est possible de filtrer la sortie de la commande cat pour afficher uniquement certaines lignes
$ cat fichier_demo | grep emma
emma surfe avec un dinosaure

$ cat fichier_demo | grep bob
bob a un chapeau rouge
```

> Il est possible de repérer le numéro des processus liés à un service avec la commande `systemctl status sshd`.

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

🌞 **Modifier le fichier de conf**

- exécutez un `echo $RANDOM` pour **demander à votre shell de vous fournir un nombre aléatoire**
  - simplement pour vous montrer la petite astuce et vous faire manipuler le shell :)
  - pour un numéro de port valide, c'est entre 1 et 65535 !
- **changez le port d'écoute du serveur SSH** pour qu'il écoute sur ce numéro de port
  - il faut modifier le fichier avec `nano` ou `vim` par exemple
  - dans le compte-rendu je veux un `cat` du fichier de conf
  - filtré par un `| grep` pour mettre en évidence la ligne que vous avez modifié
- **gérer le firewall**
  - c'est du réseau, donc c'est dans le [Mémo Réseau Rocky](../../cours/memo/rocky.md)
  - fermer l'ancien port
  - ouvrir le nouveau port
  - vérifier avec un `firewall-cmd --list-all` que le port est bien ouvert
    - vous filtrerez la sortie de la commande avec un `| grep TEXTE`

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