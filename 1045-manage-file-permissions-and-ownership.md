# 104.5. Gestion des permissions et de la propriété sur les fichiers

**Poids :** 3

**Description:** Les candidats doivent être en mesure de contrôler l'accès aux fichiers en utilisant les droits d'accès et les propriétés appropriés.

**Connaissances clés:**

* Gestion des permissions d'accès sur les fichiers standards et les fichiers spéciaux, ainsi que sur les répertoires.
* Utilisation des modes d'accès comme suid, sgid et sticky bit pour maintenir la sécurité.
* Savoir changer le masque de création des fichiers par défaut.
* Utilisation du champ groupe pour attribuer les permissions aux membres d'un groupe. 

**Concepts et Utilitaires :**

* chmod
* umask
* chown
* chgrp

####  <a href="users-groups-and-file-ownership" id="users-groups-and-file-ownership"></a>

#### Utilisateurs, groupes et propriétaire de fichier <a href="users-groups-and-file-ownership" id="users-groups-and-file-ownership"></a>

Jusqu'ici, nous savons que Linux est un système multi-utilisateurs et que chaque utilisateur appartien à un groupe primaire et éventuellement à d'autres groupes. Il est aussi possible de se connecter avec un utilisateur et de devenir un autre utilisateur en utilisant la commande `su`. La propriété des fichiers dans Linux et l'autorisation d'accès sont liés aux id des utilisateurs et aux groupes.

### Utilisateur et groupes <a href="user-and-groups" id="user-and-groups"></a>

Pour commencer, revoyons quelques informations basique sur l'utilisateur et le groupe via certaines commandes :

* whoami : Affiche le nom de l'utilisateur actuellement connecté

```
user1@ubuntu16-1:~/sandbox$ whoami
user1
user1@ubuntu16-1:~/sandbox$ su -
Password: 
root@ubuntu16-1:~# whoami
root
```

* groups: Nous pouvons trouver dans quels groupes nous sommes en utilisant la commande `groups`.

```
root@ubuntu16-1:~# groups
root
root@ubuntu16-1:~# exit
logout
user1@ubuntu16-1:~/sandbox$ groups
user1
```

* id :  Nous pouvons trouver à la fois les information sur l'utilisateur et son groupe avec la commande `id`.

```
user1@ubuntu16-1:~/sandbox$ id
uid=1001(user1) gid=1001(user1) groups=1001(user1)
user1@ubuntu16-1:~/sandbox$ su -
Password: 
root@ubuntu16-1:~# id
uid=0(root) gid=0(root) groups=0(root)
```

> Il peut montrer l'ID numérique (UID ou group ID) de l'utilisateur courant ou d'un autre utilisateur sur le serveur.

{% hint style="info" %}
Les informations sur les utilisateurs et les groupes sont stockées dans /etc/passwd et /etc/group parmi d'autres informations.

```
root@ubuntu16-1:~# cat /etc/passwd | grep user1
user1:x:1001:1001::/home/user1:
root@ubuntu16-1:~# cat /etc/group | grep user1
user1:x:1001:
```
{% endhint %}

#### Propriété des fichiers et permissions

Chaque fichier sur un système Linux a un propriétaire et un groupe qui lui sont associé.

 Utiliser la commande `ls-l` pour afficher le propriétaire et le groupe.

```
user1@ubuntu16-1:~/sandbox$ ls -l
total 4
drwxrwxr-x 2 user1 user1 4096 Jan 27 21:49 dir1
-rw-rw-r-- 1 user1 user1    0 Jan 27 21:49 file1
```

> Comme vous pouvez voir, le file1 appartient à  user1 et a un groupe appelé user1.

Le premier caractère d'une longue liste décrit le type d'objet. "-" pour un fichier régulier, "d" pour un dossier, "l" pour un lien symbolique (nous les verrons plus tard).

![](.gitbook/assets/permis-filepermis.jpg)

Les permissions sont spécifié séparemment pour le propriétaire du fichier, les membres du groupe du fichier et n'importe qui d'autres. 

Le modèle de permission de Linux a trois types de permissions pour chaque objet du système de fichier.

Les permissions sont lire(r), écrire(w), et exécuter (x). La permission d'écrire inclut la possibilité de modifier ou de supprimer un objet. Un `-` indique que la permission correspondante n'est pas accordée. Exemple:

```
root@ubuntu16-1:~# ls -l /sbin/fsck
-rwxr-xr-x 1 root root 44184 May 16  2018 /sbin/fsck
```

Commme bou le voyez fsck peut être lu, écrit et exécuté par son propriétaire (root) et tous les membres du groupe root, mais les autres peuvent seulement lire et exécuter celui-ci

#### Propriété et permission des dossiers <a href="directories" id="directories"></a>

![](.gitbook/assets/permis-dirpermis.jpg)

Les dossiers utilises les mêmes flags de permission que les fichiers régulier, mais ils sont interprétés différement.

* La permission de lire pour un dossier permet à un utilisateur avec cette permission de lister le contenu de ce dossier.
* La permission d'écire signifie qu'un utilisateur avec cette permission peut créer ou supprimer des fichiers dans ce dossier.
* La permission d'exécuter permet à l'utilisateur d'entrer dans le dossier et d'accéder aux sous-dossiers.

Sans la permission d'éxécuter sur un dossier, les objets du système de fichier dans un dossier ne sont pas accessible. Sans la permission de lire sur un dossier, le système de fichier à l'intérieur ne peut pas être vu dans une liste de dossier, mais ces objets peuvent encore être accédés tant que vous connaisez le chemin complet de l'objet sur le disque. Exemple:

```
root@ubuntu16-1:~# ls -l /home/
total 8
drwxr-xr-x 22 payam payam 4096 Oct 27  2018 payam
drwxr-xr-x 19 user1 user1 4096 Jan 27 21:49 user1
```

Le premier caractère indique que c'est un dossier. Le propriétaire (user1) a les droits de lire, écrire, éxécuter mais les autres membres du groupes user1 et les autres ont seulement le droit de lire et d'éxecuter sur ce dossier, (comme mentionné, exécuter nous laisse voir les fichiers à l'intérieur )

#### Modifier les permissions

### chmod

La commande que vous devez utiliser pour modifier les permissions sur les fichiers est appelée chmod , ce qui signifie “change mode". Il y a deux façon de dire à cette commande ce qu'elle doit faire:

* en utilisant les codes raccourcis
* en utilisant les codes en octal

**1- En utilisant les codes raccourcis:**  C'est la manière la plus simple. 

![](.gitbook/assets/permis-chmodshortcodes.jpg)

```
chmod [reference][operator][mode] file... 
```

Les références peuvent être:

* u pour l'utilisateur  (propriétaire du fichier)
* g pour groupe (les utilisateurs qui sont membre du groupe nommé)
* o pour autres (others) (les utilisateurs qui ne sont pas propriétaires du fichier ou membre du groupe du fichier)
* a pour tous(all) (Tous ceux mentionné ci-dessus, équivalent à ugo)

Les opérateurs peuvent être

* \+  Ajoute le mode spécifié à la classe spécifié
*  \- Supprime le mode spécifié à la classe spécifié
* \= Le mode spécifié doit être le même pour les classes spécifiés

Et les modes doivent être

* r  : Permission de lire le fichier
* w : Permission d'écrire le fichier (ou de le supprimer).
* x : Permission d'exécuter le fichier, ou, dans le cas d'un dossier, de rechercher à l'intérieur.

```
user1@ubuntu16-1:~/sandbox$ ls -l | grep file1
-rw-rw-r-- 1 user1 user1    0 Jan 27 21:49 file1

user1@ubuntu16-1:~/sandbox$ chmod u+x file1

user1@ubuntu16-1:~/sandbox$ ls -l | grep file1
-rwxrw-r-- 1 user1 user1    0 Jan 27 21:49 file1

user1@ubuntu16-1:~/sandbox$ chmod o-r file1

user1@ubuntu16-1:~/sandbox$ ls -l | grep file1
-rwxrw---- 1 user1 user1    0 Jan 27 21:49 file1
```

>  Si nous voulons utiliser différents ensembles de permissions pour l'utilisateur, le groupe et les autres, nous pouvons utiliser des expressions différentes séparées par des virgules — par exemple, `ug=rwx,o=rx`

```
user1@ubuntu16-1:~/sandbox$ ls -l | grep file1
-rwxrw---- 1 user1 user1    0 Jan 27 21:49 file1

user1@ubuntu16-1:~/sandbox$ chmod u-x,g+x,o+r file1

user1@ubuntu16-1:~/sandbox$ ls -l | grep file1
-rw-rwxr-- 1 user1 user1    0 Jan 27 21:49 file1
```

> Utiliser a comme ugo avec l'opérateur = pour définir un mode exact est plus facile

```
user1@ubuntu16-1:~/sandbox$ ls -l | grep file1
-rw-rwxr-- 1 user1 user1    0 Jan 27 21:49 file1
user1@ubuntu16-1:~/sandbox$ chmod a=rw file1
user1@ubuntu16-1:~/sandbox$ ls -l | grep file1
-rw-rw-rw- 1 user1 user1    0 Jan 27 21:49 file1
```

**2- En utilisant les code octal :** Jusqu'ici nous avons utilisé les symboles (ugoa et rxw) pour définir les permissions. Nous pouvons aussi définir les permissions en utilisant les nombre octal à la place des symboles.

![](.gitbook/assets/permis-chmodoctalcodes.jpg)

Pour utiliser les codes octal avec chmod nous devons créers une chaine octal, et ce n'est rien de plus qu'un simple somme de nombre:

| Symbolic | note  | Octal |
| -------- | ----- | ----- |
| rwx      | 4+2+1 | 7     |
| rw-      | 4+2   | 6     |
| r-x      | 4+1   | 5     |
| r--      | 4     | 4     |
| -wx      | 2+1   | 3     |
| -w-      | 2     | 2     |
| --x      | 1     | 1     |
| ---      | 0     | 0     |

```
user1@ubuntu16-1:~/sandbox$ ls -l | grep file1
-rw-rw-rw- 1 user1 user1    0 Jan 27 21:49 file1

user1@ubuntu16-1:~/sandbox$ chmod 700 file1

user1@ubuntu16-1:~/sandbox$ ls -l | grep file1
-rwx------ 1 user1 user1    0 Jan 27 21:49 file1

user1@ubuntu16-1:~/sandbox$ chmod 655 file1

user1@ubuntu16-1:~/sandbox$ ls -l | grep file1
-rw-r-xr-x 1 user1 user1    0 Jan 27 21:49 file1
```

Pour modifier les permissions de manière récursive sur les dossiers et fichiers utiliser l'option `-R` :

```
user1@ubuntu16-1:~/sandbox$ chmor -R o+r dir1
```

#### Mode d'accès

Lorsque nous nous conenctons, le nouveau processus shell se lance avec votre ID utilisateur et votre ID de groupe. Cela signifie généralement que vous ne pouvez pas accéder aux fichiers qui appartiennent aux autres et ne pouvea pas écrire sur les fichiers du système. D'un autre côté, les utilisateurs sont totalement dépendant d'autres programmes pour faire les opérations. 

Un exemple important est le fichier /etc/passwd, qui ne peut pas être modifier par un utilisater normal directement, car la permission d'écriture est activé seulement pour root. Cependant, si les utilisateurs normaux ont besoin de modifier le fichier, voici comment faire :

```
root@ubuntu16-1:~# which passwd
/usr/bin/passwd
root@ubuntu16-1:~# ls -l /usr/bin/passwd
-rwsr-xr-x 1 root root 54256 May 16  2017 /usr/bin/passwd
```

Donc, si l'utilisateur n'est pas capable de modifier ce fichier, comment cela peut être fait ? Que veut dire le "s" ?

### suid , guid

Le modèle de permissions de Linux a deux modes d'accès spéciaux appelé suid (set user id) et sgid (set group id). Lorsqu'un programme exécutable a le mode d'accès suid défini, il sera lancé comme s'il avait été lancé par le propriétaire du fichier, plutôt que par l'utilisateur qui l'a réellement démarré. De manière similaire, avec le mode d'accès sgid défini, le programme sera lancé avec l'utilisateur faisant partie du groupe du fichier plutôt que son propre groupe.

> #### Dossiers et  sgid <a href="directories-and-sgid" id="directories-and-sgid"></a>
>
> Lorsqu'un dossier a le mode sgid activé, tous fichiers et dossiers à l'intérieur hérite de l'ID du groupe du dossier. C'est particulièrement utile pour les arbres de dossiers qui sont utilisé par un groupe de personne qui travaillent sur le même projet.

### sticky bit

Nous avons vu comment n'importe qui avec la permission d'écrire dans un dossier peut supprimer les fichiers à l'intérieur. Cela peut être acceptable pour un projet de groupe, mais pas souhaitable pour un espace de fichiers partagé comme le dossier /tmp. Heureusement, il y a une solution. Ce qui est appelé le _sticky_ bit.

Si stickybit est défini pour un dossier, il permet seulement au propriétaire ou à l'administrateur(root) de supprimer ou de supprimer le lien d'un fichier. 

Résumons ce que nous avons appris :

| Mode d'accès    | **Sur les fichiers**                   | **Sur les dossiers**                         |
| -------------- | --------------------------------------- | -------------------------------------------- |
| **SUID**       | Exécute avec les permissions du propriétaire | rien                                      |
| **GUID**       | Exécute avec les permissions du groupe  | Les nouveaux fichiers ont le groupe du dossier |
| **Sticky Bit** | rien                                 | seulement le propriétaire peut supprimer des fichiers |

#### Comment suid, guid et stickybit sont mis en place ?

Comme il n'y pas qu'une manière de définir les modes d'accès, le caractère d'exécution est utilisé. La lettre "s" es utilisé à la fois pour les suid et guid mais la lettre "t" est pour stickybit. Nous utilisons encore `+/-` pour ajouter/supprimer les permissions.

![](.gitbook/assets/permis-accessmodes.jpg)

> Comme vous l'avez déjà remarquer, si le fichier ou le dossier est déjà exécutable  **s** et **t** s'affichent après les modes d'accès. 
>
> Mais si le fichier ou le dossier n'est pas encore exécutable avant de définir le mode d'accès,**S** et **T** apparaitront.

Comme exemple pour suid penser à la commande ping, comme ping a besoin d'accder au carte réseau il a besoin des permissions root, mais un utilisateur ordinatire peut l'utiliser:

```
user1@ubuntu16-1:~/sandbox$ which ping
/bin/ping
user1@ubuntu16-1:~/sandbox$ ls -l /bin/ping
-rwsr-xr-x 1 root root 44168 May  7  2014 /bin/ping
user1@ubuntu16-1:~/sandbox$ ping 8.8.8.8 -c2
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=128 time=39.9 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=128 time=39.7 ms

--- 8.8.8.8 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 39.793/39.862/39.932/0.211 ms
```

Maintenant essayons de définir guid sur un dossier et de créer un fichier à l'intérieur avec un autre utilisateur:

```
user1@ubuntu16-1:~/sandbox$ ls -l | grep dir1
drwxrwxrwx 2 user1 user1 4096 Jan 27 21:49 dir1
user1@ubuntu16-1:~/sandbox$ chmod g+s dir1
user1@ubuntu16-1:~/sandbox$ ls -l | grep dir1
drwxrwsrwx 2 user1 user1 4096 Jan 27 21:49 dir1
```

```
user2@ubuntu16-1:~$ whoami
user2
user2@ubuntu16-1:~$ cd /home/user1/sandbox/dir1/
user2@ubuntu16-1:/home/user1/sandbox/dir1$ touch NewFileUser2
user2@ubuntu16-1:/home/user1/sandbox/dir1$ ls -l
total 0
-rw-rw-r-- 1 user2 user1 0 Jan 29 02:02 NewFileUser2
```

Et finalement, testons comment fonctionne stickybit sur  /tmp:

```
user1@ubuntu16-1:/$ cd /
user1@ubuntu16-1:/$ ls -l | grep tmp
drwxrwxrwt  22 root  root   4096 Jan 29 02:03 tmp
user1@ubuntu16-1:/$ cd tmp/
user1@ubuntu16-1:/tmp$ touch NewFileUser1
user1@ubuntu16-1:/tmp$ ls -l | grep -i newfile
-rw-rw-r-- 1 user1 user1    0 Jan 29 02:04 NewFileUser1
```

```
user2@ubuntu16-1:~$ whoami
user2
user2@ubuntu16-1:~$ cd /tmp/
user2@ubuntu16-1:/tmp$ rm NewFileUser1 
rm: remove write-protected regular empty file 'NewFileUser1'? y
rm: cannot remove 'NewFileUser1': Operation not permitted
```

#### Définir les modes d'accès via les codes octal:

Nous pouvons aussi utiliser les codes octal pour définir suid, guid et stickybit:

| Access Mode   | octal |
| ------------- | ----- |
| **SUID**      | 4000  |
| **GUID**      | 2000  |
| **StickyBit** | 1000  |

Et de nouveau nous faisons la somme des chiffres.

```
###SUID
user1@ubuntu16-1:~/sandbox$ touch file2
user1@ubuntu16-1:~/sandbox$ ls -l | grep file2
-rw-rw-r-- 1 user1 user1    0 Jan 29 02:32 file2
user1@ubuntu16-1:~/sandbox$ chmod 4470 file2
user1@ubuntu16-1:~/sandbox$ ls -l | grep file2
-r-Srwx--- 1 user1 user1    0 Jan 29 02:32 file2

###GUID
user1@ubuntu16-1:~/sandbox$ mkdir dir2
user1@ubuntu16-1:~/sandbox$ ls -l | grep dir2
drwxrwxr-x 2 user1 user1 4096 Jan 29 02:33 dir2
user1@ubuntu16-1:~/sandbox$ chmod 2770 dir2
user1@ubuntu16-1:~/sandbox$ ls -l | grep dir2
drwxrws--- 2 user1 user1 4096 Jan 29 02:33 dir2

###StickyBit
user1@ubuntu16-1:~/sandbox$ chmod 1770 dir2
user1@ubuntu16-1:~/sandbox$ ls -l | grep dir2
drwxrws--T 2 user1 user1 4096 Jan 29 02:33 dir2
```

### umask

Lorsqu'un nouveau fichier ou dossier est créé, le processus de création spécifie les permissions que ce nouveau fichier ou dossier doit avoir. D'où viennent elles ? Elles viennent de umask.

 Vous pouvez voir votre paramètrage de umask avec la commande `umask` :

```
root@ubuntu16-1:~# umask
0022
```

#### Comment fonctionne umask ?

![](.gitbook/assets/permis-umask.jpg)

 Lorsqu'un fichier est crée, le processus de création spécifie les permissions que le nouveau fichier doit avoir. Souvent, le mode requis est 0666, ce qui rend le fichier accessible en lecture / écriture par tout le monde (mais pas executable). Les dossiers utilise par défaut 0777. Cependant, cette création permissive est affectée par une valeur _umask_, qui peut définir quel permissions un utilisateur ne veut **pas** donner automatiquement lors de la création de fichiers ou de dossiers. Le système utilise la valeur umask pour réduire les permissions originales demandées.

```
user1@ubuntu16-1:~/sandbox$ umask
0002

user1@ubuntu16-1:~/sandbox$ touch newfile

user1@ubuntu16-1:~/sandbox$ ls -l | grep newfile
-rw-rw-r-- 1 user1 user1    0 Jan 28 05:44 newfile

user1@ubuntu16-1:~/sandbox$ mkdir newdir
user1@ubuntu16-1:~/sandbox$ ls -l | grep newdir
drwxrwxr-x 2 user1 user1 4096 Jan 28 05:45 newdir
```

Généralement umask est défini de manière générale pour le système (il peut être défini par utilisateur) et nous pouvons trouver sa configuration dans un de ces endroits (en fonction de votre distribution de Linux): 

> * /etc/profile (usually)
> * /etc/bashrc (usually)
> * /etc/logindefs (ubuntu)

Sur ubuntu regardons dans /etc/logindefs:

```
# If USERGROUPS_ENAB is set to "yes", that will modify this UMASK default value
# for private user groups, i. e. the uid is the same as gid, and username is
# the same as the primary group name: for these, the user permissions will be
# used as group permissions, e. g. 022 will become 002.
#
# Prefix these values with "0" to get octal, "0x" to get hexadecimal.
#
ERASECHAR       0177
KILLCHAR        025
UMASK           022
```

Cela dit que umask sera 002 si USERGROUPS_ENAB est défini, vérifions le :

```
root@ubuntu16-1:~# cat /etc/login.defs | grep -i USERGROUPS_ENAB
# If USERGROUPS_ENAB is set to "yes", that will modify this UMASK default value
USERGROUPS_ENAB yes
```

C'est pour ça que umasl est à  002 sur notre système.

#### Définir le propriétaire de fichier et le groupe

Tous les fichiers dans Linux appartiennent à un propriétaire et à un groupe. Nous pouvons définir le propriétaire avec la commande `chown`, et le groupe avec la commande `chgrp `.

### chown

 L'utilisateur root peut modifier la propriété d'un ficheir en utilisant la commande `chown`. Nous pouvons utiliser le nom de l'utilisateur ou son User ID.

```
chown [OPTION]… [OWNER][:[GROUP]] FILE…
```

Le groupe du fichier peut être modifier en même temps en ajoutant un deux-points et le nom du groupe ou son ID juste après le nom de l'utilisateur ou son ID.

```
root@ubuntu16-1:~/sandbox# touch file1
root@ubuntu16-1:~/sandbox# ls -l
total 0
-rw-r--r-- 1 root root 0 Jan 29 03:00 file1
root@ubuntu16-1:~/sandbox# chown user1 file1 
root@ubuntu16-1:~/sandbox# ls -l
total 0
-rw-r--r-- 1 user1 root 0 Jan 29 03:00 file1
```

Si seul le deux-points est donné, alors le groupe par défaut de l'utilisateur est utilisé :

```
root@ubuntu16-1:~/sandbox# chown user1: file1
root@ubuntu16-1:~/sandbox# ls -l
total 0
-rw-r--r-- 1 user1 user1 0 Jan 29 03:00 file1
```

 L'option -R appliquera les modifications de manière récursive et `  -c  ` apporte lorsqu'une modification de fichier est faite. Nous pouvons aussi utiliser d'autres propriétaire de ficheir via l'option  `--reference` .

### chgrp

 La commande chgrp dans Linux est utilisé pour modifier le groupe propriétaire d'un fichier ou d'un dossier.

```
chgrp [OPTION]… GROUP FILE…
```

**Note1:** Nous avons besoin des permissions d'administrateur pour ajouter ou supprimer des groupes 

```
root@ubuntu16-1:~/sandbox# ls -l
total 0
-rw-r--r-- 1 user1 user1 0 Jan 29 03:00 file1
root@ubuntu16-1:~/sandbox# chgrp user2 file1
root@ubuntu16-1:~/sandbox# ls -l
total 0
-rw-r--r-- 1 user1 user2 0 Jan 29 03:00 file1
root@ubuntu16-1:~/sandbox# chgrp root file1
root@ubuntu16-1:~/sandbox# ls -l
total 0
-rw-r--r-- 1 user1 root 0 Jan 29 03:00 file1
```

**Note2:** Le propriétaire d'un fichier peut toujours modifier le groupe de son fichier ou dossier pour son propre groupe ou un des groupes dont il est membre.

```
root@ubuntu16-1:~# cd ~user1/

root@ubuntu16-1:/home/user1# cd sandbox/

root@ubuntu16-1:/home/user1/sandbox# ls -l
total 0
-rw-r--r-- 1 root root 0 Jan 29 05:21 file1

root@ubuntu16-1:/home/user1/sandbox# chown user1 file1 
```

```
user1@ubuntu16-1:~/sandbox$ whoami
user1

user1@ubuntu16-1:~/sandbox$ id
uid=1001(user1) gid=1001(user1) groups=1001(user1)

user1@ubuntu16-1:~/sandbox$ ls -l
total 0
-rw-r--r-- 1 user1 root 0 Jan 29 05:21 file1

user1@ubuntu16-1:~/sandbox$ chgrp user1 file1

user1@ubuntu16-1:~/sandbox$ ls -l
total 0
-rw-r--r-- 1 user1 user1 0 Jan 29 05:21 file1
```

Comme pour de nombreuses commande de cette leçon, `chgrp` a une option `-R` qui permet aux modifications d'être appliqués de manière récursive sur tous les fichiers selectionnés et les sous-dossiers.

\--reference  Utilise le nom du groupe comme un fichier référence pour modifier le groupe d'un autre fichier ou dossier.

.

.

.

{% hint style="success" %}
**Groupes primaires et secondaire**

Il y a actuellement deux types de groupes — **primaire** et **secondaire**.

Le **groupe primaire** est celui qui est enregistré dans le fichier **/etc/passwd**, configuré lorsqu'un compte est créé. Lorsqu'un utilisateur crée un fichier, c'est son groupe primaire qui est associé au fichier. 

 Les **Groupes secondaires** sont ceux dans lesquels l'utilisateur a pu être ajouté. L'appartenance au groupes secondaires est défini dans le fichier /etc/group .

Un utilisateur peut modifier son **groupe primaire** (**groupe par défaut**) avec la commande `newgrp `, et après ça tous les fichiers/dossiers que l'utilisateur créera auront ce groupe.
{% endhint %}



.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-104-5/](https://developer.ibm.com/tutorials/l-lpic1-104-5/)

[https://jadi.gitbooks.io/lpic1/content/1045\_manage_file_permissions_and_ownership.html](https://jadi.gitbooks.io/lpic1/content/1045\_manage_file_permissions_and_ownership.html)

[https://www.geeksforgeeks.org/chmod-command-linux/](https://www.geeksforgeeks.org/chmod-command-linux/)

[https://www.geeksforgeeks.org/permissions-in-linux/](https://www.geeksforgeeks.org/permissions-in-linux/)

[https://www.geeksforgeeks.org/chown-command-in-linux-with-examples/](https://www.geeksforgeeks.org/chown-command-in-linux-with-examples/)

[https://www.geeksforgeeks.org/chgrp-command-in-linux-with-examples/](https://www.geeksforgeeks.org/chgrp-command-in-linux-with-examples/)

[https://www.networkworld.com/article/3409781/mastering-user-groups-on-linux.html](https://www.networkworld.com/article/3409781/mastering-user-groups-on-linux.html)

.

.

.
