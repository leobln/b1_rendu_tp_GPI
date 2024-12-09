# Partie I : Des beaux one-liners

🌞 **Afficher la quantité d'espace disque disponible**

```
[leobln@node1 ~]$ df -h | grep 'root' | tr -s ' ' | cut -d ' ' -f4
16G
```

🌞 **Afficher combien de fichiers il est possible de créer**

```
[leobln@node1 ~]$ df -i | grep 'root' | tr -s ' ' | cut -d ' ' -f2
8910848
```

🌞 **Afficher l'heure et la date**

```
[leobln@node1 ~]$ date +"%d/%m/%Y %H:%M:%S"
09/12/2024 15:38:11
```

🌞 **Afficher la version de l'OS précise**

```
[leobln@node1 ~]$ source /etc/os-release
[leobln@node1 ~]$ echo "$PRETTY_NAME"
Rocky Linux 9.5 (Blue Onyx)
```

🌞 **Afficher la version du kernel en cours d'utilisation précise**

```
[leobln@node1 ~]$ uname -r
5.14.0-503.14.1.el9_5.x86_64
```

🌞 **Afficher le chemin vers la commande `python3`**

```
[leobln@node1 ~]$ which python3
/usr/bin/python3
```

🌞 **Afficher l'utilisateur actuellement connecté**

```
[leobln@node1 ~]$ env | grep leobln
...
[leobln@node1 ~]$ echo $USER
leobln
```

🌞 **Afficher le shell par défaut de votre utilisateur actuellement connecté**

```
[leobln@node1 ~]$ cat /etc/passwd | grep $USER | cut -d':' -f7
/bin/bash
```

🌞 **Afficher le nombre de paquets installés**

```
[leobln@node1 ~]$ rpm -qa | wc -l
362
```

🌞 **Afficher le nombre de ports en écoute**

```
[leobln@node1 ~]$ sudo ss -ltupn | grep -v Netid | tr -s ' '  | grep -v '::'  | cut -d':' -f2 | cut -d' ' -f1 | wc -l
3
```

# Partie II : Un premier ptit script


🌞 **Ecrire un script qui produit exactement l'affichage demandé**


🌞 **Le script `id.sh` affiche l'état du firewall**

🌞 **Le script `id.sh` affiche l'URL vers une photo de chat random**

🌞 **Stocker le fichier `id.sh` dans `/opt`**

🌞 **Prouvez que tout le monde peut exécuter le script**

🌞 **Ajouter l'exécution au `.bashrc` de votre utilisateur**

