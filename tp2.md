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

```
#!/bin/bash

user=$(echo $USER)
date=$(date +"%d/%m/%Y %H:%M:%S")
shel=$(cat /etc/passwd | grep $USER | cut -d':' -f7)
source /etc/os-release
os=$(echo "$PRETTY_NAME")
disque=$(df -h | grep 'root' | tr -s ' ' | cut -d ' ' -f4)
inode=$(df -i | grep 'root' | tr -s ' ' | cut -d ' ' -f2)
packet=$(rpm -qa | wc -l)
port=$(sudo ss -ltupn | grep -v Netid | tr -s ' '  | grep -v '::'  | cut -d':' -f2 | cut -d' ' -f1 | wc -l)
ram=$( free -h | grep 'Mem' | tr -s ' ' | cut -d ' ' -f7)
karnel=$(uname -r)
python=$(which python3)

echo "Salu a toa $user.
Nouvelle connexion $date.
Connecté avec le shell $shel.
OS : $os - Kernel : $karnel
Ressources :
  - $ram RAM dispo
  - $disque espace disque dispo
  - $inode fichiers restants
Actuellement : 
  - $packet paquets installés
  - $port port(s) ouvert(s)
Python est bien installé sur la machine au chemin : $python"
```

🌞 **Le script `id.sh` affiche l'état du firewall**

```
#!/bin/bash

firewall=$(systemctl is-active firewalld)

if [[ $firewall -eq "active" ]]
then
    echo "le firewall est actif"
else
    echo "le firewall n'est pas actif"
fi
```

🌞 **Le script `id.sh` affiche l'URL vers une photo de chat random**

```
#!/bin/bash

firewall=$(systemctl is-active firewalld)
chat=$(curl https://api.thecatapi.com/v1/images/search -s | cut -d'"' -f8)

if [[ $firewall -eq "active" ]]
then
    echo "le firewall est actife"
else
    echo "le firewall n'est pas actife"
fi

echo "Voilà ta photo de chat $chat "
```

🌞 **Stocker le fichier `id.sh` dans `/opt`**

``` 
[leobln@node1 ~]$ sudo mv id.sh /opt/
```

🌞 **Prouvez que tout le monde peut exécuter le script**

```
[leobln@node1 ~]$ ls -al /opt/id.sh
-rwxr-xr-x. 1 leobln leobln 288 Dec  9 18:24 /opt/id.sh
```

🌞 **Ajouter l'exécution au `.bashrc` de votre utilisateur**

```
[leobln@node1 ~]$ exit
logout
Connection to 10.1.1.1 closed.
PS C:\Users\leobln> ssh leobln@10.1.1.1
leobln@10.1.1.1's password:
Last login: Mon Dec  9 18:39:50 2024 from 10.1.1.3
[sudo] password for leobln:
Salu a toa leobln.
Nouvelle connexion 09/12/2024 18:40:26.
Connecté avec le shell /bin/bash.
OS : Rocky Linux 9.5 (Blue Onyx) - Kernel : 5.14.0-503.14.1.el9_5.x86_64
Ressources :
  - 1.2Gi RAM dispo
  - 16G espace disque dispo
  - 8910848 fichiers restants
Actuellement :
  - 363 paquets installés
  - 4 port(s) ouvert(s)
Python est bien installé sur la machine au chemin : /usr/bin/python3
le firewall est actife
Voilà ta photo de chat https://cdn2.thecatapi.com/images/62t.jpg
[leobln@node1 ~]$
```

