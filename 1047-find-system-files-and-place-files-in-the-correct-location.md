# 104.7. Recherche de fichiers et placement des fichiers aux endroits adéquats

**Poids:** 2

**Description:** Les candidats doivent être parfaitement familiarisés avec la norme de la hiérarchie des systèmes de fichiers (FHS - Filesystem Hierarchy Standard), y compris le placement adéquat des fichiers et l'organisation des répertoires.

**Connaissances clés :**

* Compréhension de l'emplacement correct des fichiers dans le FHS.
* Recherche de fichiers et de commandes sur un système Linux
* Connaissance de l'emplacement et du but des fichiers et des répertoires importants tels que définis dans la FHS. 

**Concepts et Utilitaires :**

* find
* locate
* updatedb
* whereis
* which
* type
* /etc/updatedb.conf

Il y a plus de 200 distributions Linux disponibles, toutes ont beaucoup de chose en commun, il doit y avoir une manière standard de placer les fichiers dans le système, et c'est la que FHS entre en jeu.

### FHS

 La hiérarchie standard du système de fichier (Filesystem Hierarchy Standard (FHS)) est un document qui spécifie le modèle commun de dossier sur un système Linux/ UNIX.

| Dosser | Objectif                                                |
| --------- | ------------------------------------------------------ |
| bin       | Commande binaires essentielles                             |
| boot      | Fichier statique du chargeur de démarrage                        |
| dev       | Fichiers de périphérique                                           |
| etc       | Configuration du système spécifique à l'hôte                     |
| lib       | Bibliothèque partagées essentielles et modules du noyau          |
| media     | Point de montage pour les médias amovibles                        |
| mnt       | Point de montage pour monter un système de fichier temporairement      |
| opt       | Paquets logiciels supplémentaires                   |
| sbin      | Binaires essentiels du système                              |
| srv       | Données pour les services fournis par ce système              |
| tmp       | Fichiers temporaire                                        |
| usr       | Hierarchie secondaire                                    |
| var       | Données variables                                          |
| home      | Dossier privé de l'utilisateur (optionnel)                       |
| root      | Dossier privée pour l'utilisateur root (optionnel)            |

{% hint style="info" %}
Les hiérarchies `/usr` et `/var` sont suffisament complexes pour avoir une section complète du FHS dédiés à elles.

Le`/usr` contient des binaires, des bibliothèques, de la documentation, du code source pour les programmes de second niveau.

* **/usr/bin** contient les fichiers binaire pour les programmes utilisateur.
* **/usr/sbin** contient les fichiers binaire pour les administrateurs systèmes.
* **/usr/lib** contient les bibliothèques pour /usr/bin et /usr/sbin
* **/usr/local** contient les programmes utilisateurs que vous installer depuis les sources.
* **/usr/src** contient le code source du noyau Linux, les fichiers entêtes et la documentation.

Le système de fichier `/var` contient des fichiers de données variables, incluant des dossiers d'enregistrement et des fichiers, des données administratives et de logs, et des fichiers temporaires. Certaines parties de `/var` ne sont pas partageable entre différents systèmes, mais les autres comme `/var/mail`, `/var/cache/man`, `/var/cache/fonts`, et `/var/spool/news`, peuvent être partagées.
{% endhint %}

### PATH

Lorsque vous lancer un programme en ligne de commande, le shell lbash rechercher dans une liste de dossier pour trouver le programme que vous avez demander. La liste des dossier est spécifié dans la variable d'environnement PATH.

```
root@ubuntu16-1:~# echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin
```

```
user1@ubuntu16-1:~$ echo $PATH
/home/user1/bin:/home/user1/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```

Comme vous le voyez, la variable PATH est juste une liste de nom de dossier, séparé par des deux-points. Noter les différences entre le path de l'utilisateur et celui du root.

Comme nous le disison dans les sections précédentes vous pouvez modifier votre patch avec la commande export `PATH=$PATH:/new/path/dir ` ou en l'ajoutant dans **.bash_profile** ou **.bashrc** .

#### which, type, et whereis

### which

La commande `which ` recherche dans le path et trouve quel commande sera exécuté lorsque vous taper cette commande.

```
root@ubuntu16-1:~# which ping
/bin/ping
```

>  La commande `which` montre la première occurence d'une commande dans votre path. Si vous voulez connaitre s'il y a plusieurs occurences, ajouter l'option `-a`

### type

 Il y a certaines commande, que la commande `which` ne trouvera pas, comme les commandes préconstruites de shell.

```
root@ubuntu16-1:~# which for
root@ubuntu16-1:~# type for
for is a shell keyword
root@ubuntu16-1:~# type type
type is a shell builtin
```

 La commande `type` est une commande préconstruite qui comprend les mots-clés de bash et peut nous dire comme une commande donné sera évalué pour l'exécution.

### whereis

 Si vous voulez plus d'information que la localisation du programme, vous pouvez utiliser la commande `whereis`.

```
root@ubuntu16-1:~# whereis ping
ping: /bin/ping /usr/share/man/man8/ping.8.gz

root@ubuntu16-1:~# whereis mkfs
mkfs: /sbin/mkfs.ext4dev /sbin/mkfs /sbin/mkfs.minix /sbin/mkfs.ext2 /sbin/mkfs.ext4 /sbin/mkfs.ext3 /sbin/mkfs.fat /sbin/mkfs.bfs /sbin/mkfs.cramfs /sbin/mkfs.ntfs /sbin/mkfs.vfat /sbin/mkfs.msdos /usr/share/man/man8/mkfs.8.gz
```

 La commande `whereis` peut aussi rechecher les pages de manuel et le code source des programmes depuis la localisation de leurs binaires.

### find

Dans une leçon précédente de cette série "104-7", vous avez appris comment rechercher les fichiers en vous basant sur leurs noms (en incluant les jokers), leurs chemins, leur taille ou leur timestamp. Dans une autre leçon de la série “104-6” vous avez appris comment trouver les liens vers un fichier particulier ou un inode.

La commande `find` est le couteau suisse des outils de recherche de fichier sur les systèmes Linux. Deux autres possibilités que vous pouvez trouver utile avec find sont sa capacité à trouver des fichiers en se basant sur son propriétaire ou le nom de son groupe et sa capacité à trouver des fichiers en se basant sur les permissions.

#### Rechercher par Propriétaire et Permissions

Nous pouvons aussi rechercher des fichiers par propriétaire du fichier ou groupe propriétaire (voir la section "104-5"). Nous le faisons en utilisant respectivement les paramètres `-user` et `-group`.

```
### finding by user
root@ubuntu16-1:~# find /tmp -user user1 | head
/tmp/gnome-software-ZPJTE0
/tmp/gnome-software-ZPJTE0/debconf.socket
/tmp/gnome-software-YDX3E0
/tmp/gnome-software-YDX3E0/debconf.socket
/tmp/gnome-software-GD8NE0

### finding by group
root@ubuntu16-1:~/test-space# find /etc  -group shadow
/etc/shadow
/etc/gshadow
```

> Utiliser les options -nouser ou -nogroup pour recherche un fichier sans utilisateur ou id de groupe.

Nous pouvons aussi rechercher des fichiers avec des permissions spécifiques. Si vous voulez correspondre exactement un ensemble de permission : `find / -perm 644`

 Si vous voulez spécifier autre chose que cette dernire permission :`find / -perm -644` .

#### Filtrer par profondeur

Nous pouvons spécifier la profondeur maximum de la recherche à partir du niveau le plus haut du dossier de recherche :

```
find -maxdepth num -name query
```

```
find -mindepth num -name query
```

Il est aussi possible de combiner les paramètres de profondeurs minimum et maximum pour se concentrer sur un interval restreint ` find -mindepth num -maxdepth num -name file`:

```

root@ubuntu16-1:~# find /tmp  -mindepth 1 -maxdepth 2 -user user1 | head
/tmp/gnome-software-ZPJTE0
/tmp/gnome-software-ZPJTE0/debconf.socket
/tmp/gnome-software-YDX3E0
/tmp/gnome-software-YDX3E0/debconf.socket
/tmp/gnome-software-GD8NE0
/tmp/gnome-software-GD8NE0/debconf.socket
/tmp/gnome-software-U5COF0
/tmp/gnome-software-U5COF0/debconf.socket
```

> Comme les autre tests, vous pouvez ajouter un ! juste avant n'importe quel phrase pour le rendre négatif. Donc nous rechercherons les fichiers qui n'appartiennent pas à user1 : `find . ! -user user1`

#### locate & updatedb

 La commande `find` recherche tous les dossiers que vous spécifier, chaque fois que vous la lancer. Pour accélérer les choses, vous pouvez utiliser une autre commande, `locate`, qui utilise une base de données pour stocker les informations de chemin plutôt que de rechercher dans le système de fichier à chaque fois.

### locate

 La commande `locate` recherche les fichiers correspondant dans une base de données qui est généralement mise à jour quotidiennement (via une tâche cron).

```
root@ubuntu16-1:~# locate bin/ls
/bin/ls
/bin/lsblk
/bin/lsmod
/sbin/lsmod
/sbin/lspcmcia
/usr/bin/lsattr
/usr/bin/lsb_release
/usr/bin/lscpu
/usr/bin/lsdiff
/usr/bin/lshw
/usr/bin/lsinitramfs
/usr/bin/lsipc
/usr/bin/lslocks
/usr/bin/lslogins
/usr/bin/lsof
/usr/bin/lspci
/usr/bin/lspgpot
/usr/bin/lsusb
/usr/lib/klibc/bin/ls
/usr/lib/plainbox-provider-checkbox/bin/lsmod_info
/usr/lib/plainbox-provider-resource-generic/bin/lsb_resource
```

 La commande `locate` trouve n'importe quel partie du nom du chemin, pas seulement le nom du fichier.

### updatedb

 La base de données par défaut utilisé par locate est stocké dans le système de fichier` /var`, dans un endroit comme `/var/lib/locatedb`. _Cela peut être différent entre les système qui utilise les paquets slocate ou mlocate pour fournir plus de sécurité ou de vitesse_. Vous pouvez trouver des statistices sur votre base de données locate en utilisant `locate -S` :

```
root@ubuntu16-1:~# locate -S
Database /var/lib/mlocate/mlocate.db:
	25,916 directories
	251,926 files
	13,669,511 bytes in file names
	6,067,728 bytes used to store database
```

 La base de donnée est créée ou mise à jour en utilisant la commande `updatedb` . (C'est génralement fait quotidiennement avec une tâche cron).

```
root@ubuntu16-1:~# updatedb
```

> Utiliser -v pour le mode verbeux pour voir ce qui se passe lors de la commande updatedb!

Le fichier `/etc/updatedb.conf`, ou parfois ` /etc/sysconfig/locate`, est le fichier de configuration de `updatedb:`

```
PRUNE_BIND_MOUNTS="yes"
# PRUNENAMES=".git .bzr .hg .svn"
PRUNEPATHS="/tmp /var/spool /media /home/.ecryptfs /var/lib/schroot"
PRUNEFS="NFS nfs nfs4 rpc_pipefs afs binfmt_misc proc smbfs autofs iso9660 ncpfs coda devpts ftpfs devfs mfs shfs sysfs cifs lustre tmpfs usbfs udf fuse.glusterfs fuse.sshfs curlftpfs ecryptfs fusesmb devtmpfs"
```

Il y a des PRUNING dans le fichier de configuration qui permet à locate de ne jamais rechercher dans ce type de fichiers ou de dossiers comme ` /tmp`  ou` /var/spool` . Vous pouvez laissez locate rechercher dans ceux ci si vous manipulez ces fichiers.

C'est terminé

## Félicitations nous avons terminé pour  LPIC1-101 !!!

.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-104-7/](https://developer.ibm.com/tutorials/l-lpic1-104-7/)

[https://www.geeksforgeeks.org/linux-file-hierarchy-structure/](https://www.geeksforgeeks.org/linux-file-hierarchy-structure/)

[https://www.digitalocean.com/community/tutorials/how-to-use-find-and-locate-to-search-for-files-on-a-linux-vps](https://www.digitalocean.com/community/tutorials/how-to-use-find-and-locate-to-search-for-files-on-a-linux-vps)[https://jadi.gitbooks.io/lpic1/content/1047\_find_system_files_and_place_files_in_the_correct_location.html](https://jadi.gitbooks.io/lpic1/content/1047\_find_system_files_and_place_files_in_the_correct_location.html)

.
