# 104.6. Création et modification des liens physiques et symboliques sur les fichiers

**Poids:** 2

**Description:** Les candidats doivent être en mesure de créer et de gérer les liens physiques et symboliques vers un fichier.

**Connaissances clés:**

* Create links
* Identify hard and/or soft links
* Copying versus linking files
* Use links to support system administration tasks

**Concepts et Utilitaires:**

* ln
* ls

### Introduction aux liens <a href="introducing-links" id="introducing-links"></a>

 Sur un périphérique de stockage, un fichier ou un dossier est une collection de blocs. Les informations à propos d'un fichier est stocké dans un _inode_, ce qui enregistre les informations comme le propriétaire, lorsque le fichier a été accédé la dernière fois, quelle taille il fait, si c'est un dossier ou non, et qui peut le lire ou l'écrire. 

 Un _entrée de dossier_ contient un nom pour un dossier ou un dossier et un pointeur vers l'inode où les informations à propos du fichier ou du dossier sont stockés.

![](.gitbook/assets/links-inodes.jpg)

>  Le numéro inode est unique dans chaque système de fichier particulier.

```
root@ubuntu16-1:~/sandbox# ls -1i
2228290 dir
2228289 file1
```

>  L'option \-i affiche le numéro d'index de chaque fichier

**Qu'est ce qu'un lien ?** Un lien est une entrée supplémentaire pour un fichier ou un dossier, permettant deux ou plus de noms pour la même chose.

#### Création de liens

Il y a deux types de liens : **Liens physiques** et **Liens symboliques**.

 Un **lien physique** est une entrée de dossier qui pointe sur un inode, alors qu'un **lien symbolique** est une entrée de dossier qui pointe vers un inode qui fournit le nom d'une autre entrée de dossier. Les liens symboliques sont aussi appelés _symlinks_.

![](.gitbook/assets/link-links.jpg)

> Les liens physique pointent vers un inode, et les inodes sont uniques dans un système de fichiers particulier, les liens physique peuvent être au travers de différents systèmes de fichier (differentes partitions ou disques durs).

> Vous pouvez créer des liens physiques pour les fichier et pas pour les dossiers. L'exception est une entrée spéciale dans le dossier vers le dossier lui-même pour son parent (. and ..)

### Liens physiques vs Liens symboliques

| Liens physiques                                                                        | Liens symboliques                                          |
| -------------------------------------------------------------------------------------- | ---------------------------------------------------------- |
| ont un numéro d'inodes.                                                               | ont des numéros d'inodes différents.          |
| Ne peut pas être croiser au travers de différents systèmes de fichiers | peut ĉroiser les systèmes de fichiers                                  |
| ne peut pas être lié au dossier                                    | nous permet de faire des liens vers les dossiers                                 |
| Les liens ont le contenu du fichier actuel  | contient le chemin vers le fichier original et non le contenu    |
| Si le fichier original est supprimé, le lien restera et montrera le contenu du fichier | La suppression du lien symbolique n'affecte rien mais lorsque le fichier original est supprimé, le lien devient 'orphelin' car il lie à un fichier non existent. |
| Les permissions seront mise à jour si nous modifions le fichier source   | Les permissions ne seront pas mise à jours   |

#### Création de liens

### ln

Nous pouvons utiliser la commande ln pour créer des liens physiques ou des liens symboliques

```
### For  Hard Link 
ln  [original filename] [link name] 

### For  Soft link 
ln  -s [original filename] [link name] 
```

### Liens physiques

 Utiliser la commande `ln` pour créer des liens physiques additionnels sur un fichier existant (mais pas pour un dossier, même si le système nous laisse faire des liens physique vers . et ..).

```
root@ubuntu16-1:~/sandbox# ls -l
total 4
drwxr-xr-x 2 root root 4096 Jan 29 08:14 dir
-rw-r--r-- 1 root root    0 Jan 29 08:14 file1

### see inodes
root@ubuntu16-1:~/sandbox# ls -1i
2228290 dir
2228289 file1

### creating Hard Link
root@ubuntu16-1:~/sandbox# ln file1 HardLink

### comparing inodes
root@ubuntu16-1:~/sandbox# ls -1i
2228290 dir
2228289 file1
2228291 file2
2228289 HardLink
```

La commande ls -l montre tous les liens avec une colonne lien qui montre le nombre de liens :

```
root@ubuntu16-1:~/sandbox# ls -l
total 4
drwxr-xr-x 2 root root 4096 Jan 29 08:14 dir
-rw-r--r-- 2 root root    0 Jan 29 08:14 file1
-rw-r--r-- 2 root root    0 Jan 29 08:14 HardLink
```

Regarder le "2" en face de file1, c'était "1" avant la création du lien physique.

### Liens symboliques

 La commande `ln` avec l'option `-s` crée un lien symbolique. Les liens symboliques utilise le nom du fichier ou du dossier, qui peut être relatif ou absolu. Si vous avez utiliser les nom relatifs, vous voudrez généralement le dossier courant pour être le dossier où vous créer le lien. Autrement, le lien sera crée de manière relative à un autre point dans le système de fichier.

```
root@ubuntu16-1:~/sandbox# touch file2

root@ubuntu16-1:~/sandbox# ls -l
total 4
drwxr-xr-x 2 root root 4096 Jan 29 08:14 dir
-rw-r--r-- 2 root root    0 Jan 29 08:14 file1
-rw-r--r-- 1 root root    0 Jan 29 09:05 file2
-rw-r--r-- 2 root root    0 Jan 29 08:14 HardLink

### see inodes
root@ubuntu16-1:~/sandbox# ls -li
total 4
2228290 drwxr-xr-x 2 root root 4096 Jan 29 08:14 dir
2228289 -rw-r--r-- 2 root root    0 Jan 29 08:14 file1
2228291 -rw-r--r-- 1 root root    0 Jan 29 09:05 file2
2228289 -rw-r--r-- 2 root root    0 Jan 29 08:14 HardLink

### creating soft link using relative path
root@ubuntu16-1:~/sandbox# ln -s  file2 SoftLink

### see inodes
root@ubuntu16-1:~/sandbox# ls -1i
2228290 dir
2228289 file1
2228291 file2
2228289 HardLink
2228292 SoftLink

### creating soft link using absolute path,we have to be in current dir)
root@ubuntu16-1:~/sandbox# ln -s dir/dir2/myconfig SoftLink2myconf

root@ubuntu16-1:~/sandbox# ls -l | grep myconfig
lrwxrwxrwx 1 root root   17 Feb  1 23:59 SoftLink2myconf -> dir/dir2/myconfig


###creating soft link to a directory
root@ubuntu16-1:~/sandbox# ln -s dir/ soft2dir

root@ubuntu16-1:~/sandbox# ls -l | grep soft2dir
lrwxrwxrwx 1 root root    4 Feb  2 00:04 soft2dir -> dir/
```

La commande `ls -l` montre tous les liens avec en valeur de la seconde colonne 1 et le lien pointe vers le fichier original.

```
drwxr-xr-x 3 root root 4096 Feb  1 23:59 dir
-rw-r--r-- 2 root root    0 Jan 29 08:14 file1
-rw-r--r-- 1 root root    0 Jan 29 09:05 file2
-rw-r--r-- 2 root root    0 Jan 29 08:14 HardLink
lrwxrwxrwx 1 root root    4 Feb  2 00:04 soft2dir -> dir/
lrwxrwxrwx 1 root root    5 Jan 29 09:06 SoftLink -> file2
lrwxrwxrwx 1 root root   17 Feb  1 23:59 SoftLink2myconf -> dir/dir2/myconfig
```

#### Liens symboliques cassés <a href="broken-symlinks" id="broken-symlinks"></a>

Puisque les liens physiques pointe toujours vers un inode qui représente un fichier, ils sont toujours valide. Cependant, les liens symboliques peuvent être cassés pour de nombreuses raisons, incluant :

* Soit le fichier original ou la cible du lient n'existe plus lorsque le lien est créé
* La cible d'un lien est supprimé ou renommé.
* Certains éléments dans le chemin de la cible est supprimé ou renommé.



{% hint style="info" %}
**Identifier les liens via la commande find**

 Pour trouver quels fichiers sont des liens physiques sur un inode particulier, utiliser la commande `find` et l'option `-samefile` avec un nom de fichier ou l'option `-inum` avec un numéro d'inode :

```
root@ubuntu16-1:~/sandbox# ls -1i
2228290 dir
2228289 file1
2228291 file2
2228289 HardLink
2228292 SoftLink

root@ubuntu16-1:~/sandbox# find . -samefile file1
./HardLink
./file1

root@ubuntu16-1:~/sandbox# find . -inum 2228289
./HardLink
./file1
```

Nous pouvons aussi utiliser la commande `find` pour rechercher les liens symboliques en utilisant l'expression `-type l` :

```
root@ubuntu16-1:~/sandbox# find . -type l 
./SoftLink
```
{% endhint %}

### Copier versus lier <a href="copying-versus-linking" id="copying-versus-linking"></a>

En fonction de ce qu'on veut accomplir, parfois nous utiliserons les liens et parfois il est mieux de faire une copie du fichier. 

* La différence majeur est que le lien fournit de plusieurs noms vers un seul fichier, alors qu'une copie crée deux jeux de données de données identifuqe sous deux noms différents. 
* Vous voudrez surement utiliser les copies pour sauvegardes et aussi pour des tests où vous voulez essayez un nouveau programme sans mettre vos données opérationnels en risque. 
* Vous utiliser les liens lorsque vous avez besoin d'un alias pour un fichier (ou un dossier), pour fournir un ou plusieurs moyens d'accès. 
* Lorsque vous mettez à jour un fichier, tous les liens sont mis à jour, ce qui n'est pas le cas dans le cas d'une copie du fichier. 

{% hint style="danger" %}
Les liens symboliques peuvent être cassé mais cela sous-entend que les opération d'écriture créer un nouveau fichier. Utiliser les liens avec précaution.
{% endhint %}

#### Les liens et le système d'administration

Les liens, particulièrement les liens symbolique, sont fréquemment utilisé dans l'administration système Linux.

1- **Les commandes d'alias pour une version particulière**

Les commandes sont souvent aliasés, donc l'utilisateur n'a pas à connaitre le numéro de version de la commande courante mais peut accéder aux autres versions par son nom long si nécessaire.

```
root@ubuntu16-1:~/sandbox# which python
/usr/bin/python
root@ubuntu16-1:~/sandbox# ls -l /usr/bin/python
lrwxrwxrwx 1 root root 9 Nov 23  2017 /usr/bin/python -> python2.7
```

2- **Exemple de commande alias**

 D'autres usages entrent en jeu lorsque les noms de commandes multiples utiliser le même code, comme les commandes variées pour arrêter ou redémarrer un système. Parfois, un nouveau nom de commande, comme `genisoimage`, remplacera un nom de commande plus vieux, mais l'ancien nom (mkisofs) est gardé comme un lien vers la nouvelle commande.

```
root@ubuntu16-1:~/sandbox# which halt
/sbin/halt
root@ubuntu16-1:~/sandbox# ls -l /sbin/halt
lrwxrwxrwx 1 root root 14 Nov 27  2018 /sbin/halt -> /bin/systemctl

root@ubuntu16-1:~/sandbox# which mkisofs 
/usr/bin/mkisofs
root@ubuntu16-1:~/sandbox# ls -l /usr/bin/mkisofs
lrwxrwxrwx 1 root root 11 Nov 26  2017 /usr/bin/mkisofs -> genisoimage
```

3- **Liens vers les bibliothèques**

Les noms de bibliothèques sont aussi gérées souvent avec des liens symboliques, soit pour autoriser les programme à être lié à un nom général pour gérer la version courante, ou gérer les systèmes comme les système 64-bit qui sont capabl de lancer des programmes 32-bit.

```
root@ubuntu16-1:~/sandbox# ls -l /usr/lib/
...
lrwxrwxrwx   1 root root     30 Jul 31  2018 libblas.so.3 -> /etc/alternatives/libblas.so.3
lrwxrwxrwx   1 root root     18 Jul  9  2011 libfsplib.so.0 -> libfsplib.so.0.0.0
-rw-r--r--   1 root root  22808 Jul  9  2011 libfsplib.so.0.0.0
lrwxrwxrwx   1 root root     19 Dec 21  2015 libgdiplus.so -> libgdiplus.so.0.0.0
lrwxrwxrwx   1 root root     19 Dec 21  2015 libgdiplus.so.0 -> libgdiplus.so.0.0.0
-rw-r--r--   1 root root 423928 Dec 21  2015 libgdiplus.so.0.0.0
drwxr-xr-x   2 root root   4096 Dec  1  2018 libmbim
-rw-r--r--   1 root root 241488 Apr 12  2016 libMonoPosixHelper.so
-rw-r--r--   1 root root 133880 Apr 12  2016 libMonoSupportW.so
lrwxrwxrwx   1 root root     17 Nov 26  2017 libnetpbm.so.10 -> libnetpbm.so.10.0
...
```

.

.

.

{% hint style="danger" %}
**Supprimer les liens symboliques**

  Nous pouvons supprimer les liens symboliques en utilisant les commandes `rm`, `unlink`, et `find`. 

_Pour supprimer un lien symbolique, vous aurez besoin d'avoir les permissions d'écriture sur le dossier qui contient le lien symbolique. Autrement, vous aurez une erreur “Operation not permitted”._

**Supprimer les liens symboliques avec `rm`**

 Pour supprimer un symlink, utiliser la commande `rm` suivi par le nom du lien symbolique comme un argument : `rm symlink_name`

 Avec `rm` vous pouvez supprimer plus d'un lien symbolique en une seule fois. Pour le faire on passe les nom des symlinks comme arguments, séparé par un espace: `rm symlink1 symlink2  ` Essayer l'option -i pour avoir une confirmation.

 Si le nom de l'argument termine avec `/`, la commande `rm` suppose que le fichier est un dossier. Une erreur survient car, sans utiliser l'option`-d` ou `-r`, `rm` ne peut pas supprimer des dossiers.

#### Supprimer les liens symbolique avec `unlink`

La commande unlink supprime un fichier! Contrairement à rm, unlink accepte seulement un argument unique.

Pour supprimer un lien symbolique, lancer la commande unkig suivi par le nom du lien sumbolique comme argument: `unlink symlink_name`

N'ajoutez pas de slash  / a la fin du nom du lien symbolique car unlink ne peut pas supprimer les dossiers.

#### Trouver et supprimer les liens symboliques cassés

Pour trouver tous les liens symboliques cassé dans un dossier, lancer la commande : `find /path/to/directory -xtype l`

 Une fois que vous avez trouvé les liens symboliques cassés, vous pouvez soit les supprimer manuellement avec `rm` ou `unlink` ou utiliser l'option `-delete` de la commande `find` : `find /path/to/directory -xtype l -delete`

#### Conclusion

Pour supprimer un lien symbolique, utiliser soit la commande `rm` ou la commande `unlink` suivi par le nom du lien symbolique comme argument. Lorsque vous enlevez un lien symbolique qui pointe sur un dossier, n'ajouter pas de slash au nom du lien symbolique.
{% endhint %}

.

[https://developer.ibm.com/tutorials/l-lpic1-104-6/](https://developer.ibm.com/tutorials/l-lpic1-104-6/)

[http://bambamdeo-linux.blogspot.com/2014/11/soft-link-and-hard-link.html](http://bambamdeo-linux.blogspot.com/2014/11/soft-link-and-hard-link.html)

[https://www.ostechnix.com/explaining-soft-link-and-hard-link-in-linux-with-examples/](https://www.ostechnix.com/explaining-soft-link-and-hard-link-in-linux-with-examples/)

[https://linoxide.com/linux-how-to/difference-soft-link-hard-link/](https://linoxide.com/linux-how-to/difference-soft-link-hard-link/)

[https://www.geeksforgeeks.org/soft-hard-links-unixlinux/](https://www.geeksforgeeks.org/soft-hard-links-unixlinux/)

``[`https://linuxize.com/post/how-to-remove-symbolic-links-in-linux/`](https://linuxize.com/post/how-to-remove-symbolic-links-in-linux/)``

.
