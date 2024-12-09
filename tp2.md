# Partie I : Des beaux one-liners

Une *one-liner* c'est le fait d'écrire en une ligne ce qu'on aurait pu écrire en plusieurs pour de la lisibilité.

Mais ça sert à quoi la lisibilité, hein ?! :d

![grep cut sed](./img/cutsedgrep.png)

## Sommaire

- [Partie I : Des beaux one-liners](#partie-i--des-beaux-one-liners)
  - [Sommaire](#sommaire)
  - [1. Intro](#1-intro)
  - [2. Let's go](#2-lets-go)

## 1. Intro

**Le but de cette première partie, c'est un échauffement, vous allez fabriquer des commandes pour aller récupérer des infos précises.**

Par exemple, si je demande : "afficher la quantité de RAM disponible en Gi", il ne faut afficher QUE la quantité de RAM dispo.

Voilà comment procéder :

```bash
# déjà, trouver une commande qui donne l'info
# pour afficher l'état de la RAM, c'est la commande free
❯ free -mh
               total        used        free      shared  buff/cache   available
Mem:           7.6Gi       3.8Gi       205Mi       731Mi       4.6Gi       3.8Gi
Swap:          8.0Gi       512Ki       8.0Gi
# l'info qu'on veut est dans la colonne tout à droite, sur la ligne du milieu

# déjà on récupère que la ligne du milieu en faisant un grep 'Mem:'
❯ free -mh | grep 'Mem:'
Mem:           7.6Gi       3.8Gi       225Mi       730Mi       4.6Gi       3.8Gi

# ensuite on remplace les espaces consécutifs par un seul espace
# ce sera utile pour la prochaine commande
❯ free -mh | grep 'Mem:' | tr -s ' '
Mem: 7.6Gi 3.7Gi 366Mi 730Mi 4.6Gi 3.8Gi

# cut permet de récupérer une seule colonne dans la ligne
# avec -d on indique le delemiter : le caractère qui définit les colonnes
# avec -f on indique le field : le numéro de la colonne qu'on veut afficher
❯ free -mh | grep 'Mem:' | tr -s ' ' | cut -d' ' -f7
3.8Gi

# et hop !
```

La réponse à "afficher la quantité de RAM disponible en Gi" serait donc : `free -mh | grep 'Mem:' | tr -s ' ' | cut -d' ' -f7`.

## 2. Let's go

Chaque 🌞 dans cette partie c'est une seule commande qui donne la réponse attendue, et uniquement la réponse attendue.

Vous n'êtes pas à limité à ça, mais je vous indique avec l'emoji 📎 la commande que je vous recommande d'utiliser pour réaliser chaque étape.

🌞 **Afficher la quantité d'espace disque disponible**

- on parle de la partition montée sur `/`
- **📎 commande `df`**

```bash
# quelque chose comme :
4G
```

🌞 **Afficher combien de fichiers il est possible de créer**

- à chaque fois qu'un fichier est créé, l'OS lui attribue un identifiant unique, appelé *inode*
- il existe une quantité limitée d'*inode* et donc, de fichiers qu'on peut créer
- **📎 commande `df`** en ajoutant la bonne option pour afficher les informations sur les *inodes*

```bash
# affiche la quantité d'inodes disponibles sur / :
893283
```

🌞 **Afficher l'heure et la date**

- au format suivant : `dd/mm/yy hh:mm:ss`
- **📎 commande `date`**

```bash
# doit afficher exactement à ce format :
08/12/24 19:23:32
```

🌞 **Afficher la version de l'OS précise**

- il y a un fichier qui contient cette info sur n'importe quel OS Linux
- le fichier c'est `/etc/os-release`
- **📎 commande `source`**

```bash
# par exemple, un truc du genre :
Rocky Linux 9.4
```

🌞 **Afficher la version du kernel en cours d'utilisation précise**

- **📎 commande `uname`**

```bash
# ça ressemble à ça (3.4 c'est vieux hihi)
3.4.10-87d57229.x86_64
```

🌞 **Afficher le chemin vers la commande `python3`**

- **📎 commande `which`**

```bash
# juste le chemin, donc :
/usr/bin/python3
```

🌞 **Afficher l'utilisateur actuellement connecté**

- à tout moment, comme on a déjà dit en cours, votre OS possède des variables qui sont définis
- genre dès que t'allumes l'OS, PAF, il définit ces variables
- on les appelle les **variables d'environnement**
- il y a notamment la variable `PATH` dont on a déjà parlé
- il existe d'autres variables, il y en a une qui contient le nom de l'utilisateur connecté
- vous pouvez utiliser la commande `env` pour voir toutes les variables d'environnement définies
- **📎 commande `echo $variable`**

```bash
# juste le nom du user donc :
it4
```

🌞 **Afficher le shell par défaut de votre utilisateur actuellement connecté**

- le fichier `/etc/passwd` contient la liste des utilisateurs, et leurs infos
- quand t'ouvres un terminal, y'a un shell qui est lancé, souvent c'est `bash` sur la plupart des OS Linux
- c'est écrit dans `/etc/passwd` quel est le shell à lancer par défaut pour tel ou tel utilisateur
- **📎 commande `cat <FICHIER> | grep $VARIABLE | ...`** : vous réutilisez la variable que vous avez trouvée précédemment pour isoler la bonne ligne

```bash
# on affiche juste le shell donc par exemple :
/bin/bash
```

🌞 **Afficher le nombre de paquets installés**

- votre OS est constitué de plein de paquets
- y'a au moins le paquet qui contient le serveur SSH !
- **📎 commande `rpm`**

```bash
# par exemple
252
```

🌞 **Afficher le nombre de ports en écoute**

- actuellement, il y a des programmes qui sont lancés et qui écoutent sur un port
- on peut consulter la liste des programmes qui écoutent sur un port
- y'a au moins le serveur SSH !
- **📎 commande `ss`**

```bash
# juste le nombre là encore :
2
```

➜ **[Lien vers la partie II.](./first.md)**
