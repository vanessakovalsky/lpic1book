# 103.3. Gestion élémentaire des fichiers

### **103.3 Gestion élémentaire des fichiers**

**Poids:** 4

**Description:** Les candidats doivent être capable d'utiliser les commandes Linux basiques pour gérer les fichiers et dossiers.

**Connaissances clés:**

* Copier, déplacer et supprimer les fichiers et dossier individuellementC
* Copier de multiples fichiers et dossier de manière récursive
* Supprimer les fichiers et les dossiers de manière récursive
* Utiliser les jokers de manière simple et avancé dans les commands
* Utiliser find pour localiser et agir sur les fichiers en se basant sur leur type, leur taille ou leur date
* Utilisation de tar, cpio et dd

**Concepts et Utilitaires:**

* cp
* find
* mkdir
* mv
* ls
* rm
* rmdir
* touch
* tar
* cpio
* dd
* file
* gzip
* gunzip
* bzip2
* xz
* file globbing

Comme nous le disions Linux est un monde de processus et de fichiers, dans cette section nous commençons à parler de gestion des fichier dans linux et nous parlerons à la fois des fichiers et des dossiers. Les outils sont vraiment basique et leur nom sont des abstraction de ce qu'ils font réellement.

### ls

ls **listse les fichiers et les dossiers**, et les métadonnées associés, comme la taille du fichier, son propriétaire et sa date de modification.

```
ls [options] [file|dir]
```

Nous pouvons utiliser des **chemins absolus** (ex:`user1@ubuntu16-1:~$ ls /home/user1/Music`) ou  des **chemins relatifs** (ex: `user1@ubuntu16-1:~$ ls Music/`) avec ls.

Sans option, ls liste les fichiers contenu dans le dossier courant, en les triant par ordre alphabétique.

```
user1@ubuntu16-1:~$ ls
Desktop    Downloads         Music     Public     Videos
Documents  examples.desktop  Pictures  Templates
```

` -1` affiche chaque résultat sur 1 ligne distincte :

```
user1@ubuntu16-1:~$ ls -1
Desktop
Documents
Downloads
examples.desktop
Music
Pictures
Public
Templates
Videos
```

La sortie par défaut de la commande ls montre seulement le nom des fichiers, ce qui n'est pas très informatif. Essayons avec  `-l` pour une liste avec un format plus long :

```
user1@ubuntu16-1:~$ ls -l
total 44
drwxr-xr-x 2 user1 user1 4096 Nov  4  2018 Desktop
drwxr-xr-x 2 user1 user1 4096 Nov  4  2018 Documents
drwxr-xr-x 2 user1 user1 4096 Nov  4  2018 Downloads
-rw-r--r-- 1 user1 user1 8980 Apr 20  2016 examples.desktop
drwxr-xr-x 2 user1 user1 4096 Nov  4  2018 Music
drwxr-xr-x 2 user1 user1 4096 Nov  4  2018 Pictures
drwxr-xr-x 2 user1 user1 4096 Nov  4  2018 Public
drwxr-xr-x 2 user1 user1 4096 Nov  4  2018 Templates
drwxr-xr-x 2 user1 user1 4096 Nov  4  2018 Videos
```

Le premier caractère représente le type de fichier : "-" pour un fichier régulier, "d" pour un dossier, "l" pour un lien symbolique (nous le verrons plus tard).

Lorsque le format long de liste est utiliser la commande ls affiche les informations suivantes ::

1. Type de fichier
2. Permissions du fichier
3. Nombre de liens physiques sur le fichier
4. Propriétaire du fichier
5. Groupe du fichier
6. Taille du fichier
7. Date et heure de dernière modifications
8. Nom du fichier

**Voir les fichiers cachés**

Par défaut, la commande ls n'affiche pas les fichiers cachés. _Dans Linux, un fichier caché est n'importe quel fichier qui commence par un point (.)_. Pour afficher tous les fichiers en incluant les fichiers cachés utiliser l'option` -a` :

```
user1@ubuntu16-1:~$ ls -a
.              .config           .gconf         Pictures   .Xauthority
..             Desktop           .gnupg         .profile   .xsession-errors
.bash_history  .dmrc             .ICEauthority  Public     .xsession-errors.old
.bash_logout   Documents         .local         .ssh
.bashrc        Downloads         .mozilla       Templates
.cache         examples.desktop  Music          Videos
```

D'autres options utiles :

| ls command options | description                                              |
| ------------------ | -------------------------------------------------------- |
| -l                 | list with long format - show permissions                 |
| -r                 | list in reverse order                                    |
| -t                 | sort by time & date                                      |
| -lh                | List Files with Human Readable Format                    |
| -F                 | List Files and Directories with ‘/’ Character at the end |
| -R                 | Recursively list Sub-Directories                         |
| -a                 | list all files including hidden file starting with '.'   |

## Copier, déplacer et supprimer <a href="copying-moving--deleting" id="copying-moving--deleting"></a>

Pour rendre cela plus simple classons les en groupes:

![](.gitbook/assets/basicfilemgmnt-table.jpg)

Avant de commencer sur comment utiliser la commande touch, démarrons en étudiant le timestamp du fichier sur Linux.

#### Le Timestamp des fichiers Linux 

Dans Linux chaque fichier est associé à un timestamp, et chaque fichier stocke les informtions de la **dernière fois ou il a été accédé**, **dernière fois où il a été modifié** et **dernière fois où il a été changé**. Donc, lorsque nous créons un nouveau fichier, que nous y accédons ou que nous modifions un fichier existant, le timestamp de ce fichier est automatiquement modifié..

Un fichier dans Linux a trois timestamps:

* **atime** (heure d'accès) - Dernière fois où le fichier a été accédé / ouvert par une commande ou application comme cat, vim ou grep.
* **mtime** (heure de modification) - Dernière fois où le contenu du fichier a été modifié.
* **ctime** (heure de changement) - Dernière fois où les attributs du fichier ou son contenu a été modifié. Les attributs inclut les permissions du fichier, son propriétaire ou sa localisation.

Pour afficher le statut du fichier incluant les timestamps, vous pouvez utiliser la commande `stat`.

```
root@ubuntu16-1:~# stat /etc/hosts
  File: '/etc/hosts'
  Size: 247           Blocks: 8          IO Block: 4096   regular file
Device: 801h/2049d    Inode: 1200795     Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2019-09-08 02:34:44.962514623 -0700
Modify: 2018-07-01 23:15:30.140815060 -0700
Change: 2018-07-01 23:15:30.144817112 -0700
 Birth: -
```

### touch

La commande touch est une commande standard utiliser dans les systèmes d'exploitation UNIX/Linux operating qui est utilisé pour ccréer, changer ou modifier le timestamp d'un fichier.

* La commande touch (sans option) créer un fichier vide s'il n'existe pas.
* Si le fichier existe la commande touche change son timestamp.

**Créer un fichier vide :**

La commande touch est utiliser pour créer un fichier sans contenu. Le fichier créé en utilisant la commande touch est vide. Cette commande peut être utilisé quand l'utilisateur n'a pas encore de données à stockées lors de la création du fichier.

```
touch file_name
```

Exemple :

```
root@ubuntu16-1:~/test-space# touch file1
root@ubuntu16-1:~/test-space# ls -l
total 0
-rw-r--r-- 1 root root 0 Sep  8 02:37 file1
```

**Créer plusieurs fichiers vide :**

```
touch File1_name File2_name File3_name
```

Par exemple :

```
root@ubuntu16-1:~/test-space# ls -l
total 0
-rw-r--r-- 1 root root 0 Sep  8 02:37 file1
root@ubuntu16-1:~/test-space# touch file1 file2 file3
root@ubuntu16-1:~/test-space# ls -l
total 0
-rw-r--r-- 1 root root 0 Sep  8 02:44 file1
-rw-r--r-- 1 root root 0 Sep  8 02:44 file2
-rw-r--r-- 1 root root 0 Sep  8 02:44 file3
```

**Eviter de créer de nouveau fichier :**

```
root@ubuntu16-1:~/test-space# touch -c file4
root@ubuntu16-1:~/test-space# ls -l
total 0
-rw-r--r-- 1 root root 0 Sep  8 02:44 file1
-rw-r--r-- 1 root root 0 Sep  8 02:44 file2
-rw-r--r-- 1 root root 0 Sep  8 02:44 file3
```

L'option `-c` dit que si le fichier n'existe pas, il ne doit pas être créé.

#### Options pour le timestamp de la commande touch :

* `-a`: change seulement le **access time** 
* `-d`: met à jour le **access et modification times**
* `-m`: change le **modification time seulement**
* `-r`: **utilise les heures d'accès et de moficiation d'un autre fichier comme référence.**
* `-t`: **créer un fichier en utilisant l'heure spécifié**

Faisons quelques modifications de timestamp.

Créer un fichier en uilisant une heure spécifique :`touch -t YYMMDDHHMM fileName`

```
root@ubuntu16-1:~/test-space# touch -t 9812010510 myfile
root@ubuntu16-1:~/test-space# ls -l | grep myfile
-rw-r--r-- 1 root root 0 Dec  1  1998 myfile
root@ubuntu16-1:~/test-space# stat myfile
  File: 'myfile'
  Size: 0             Blocks: 0          IO Block: 4096   regular empty file
Device: 801h/2049d    Inode: 2228284     Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 1998-12-01 05:10:00.000000000 -0800
Modify: 1998-12-01 05:10:00.000000000 -0800
Change: 2019-09-08 03:19:28.410459835 -0700
 Birth: -
```

Changer le access time:`touch -a fileName`

```
root@ubuntu16-1:~/test-space# touch -a  myfile
```

Cela met à jour seulement l'access time .

```
root@ubuntu16-1:~/test-space# stat myfile
  File: 'myfile'
  Size: 0             Blocks: 0          IO Block: 4096   regular empty file
Device: 801h/2049d    Inode: 2228284     Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2019-09-08 03:26:42.378185145 -0700
Modify: 1998-12-01 05:10:00.000000000 -0800
Change: 2019-09-08 03:26:42.378185145 -0700
```

Changer le modification time:`touch -m fileName`

```
root@ubuntu16-1:~/test-space# touch -m myfile
```

Cela met à jour seulement le modification time.

```
root@ubuntu16-1:~/test-space# stat myfile 
  File: 'myfile'
  Size: 0             Blocks: 0          IO Block: 4096   regular empty file
Device: 801h/2049d    Inode: 2228284     Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2019-09-08 03:26:42.378185145 -0700
Modify: 2019-09-08 03:28:06.314537292 -0700
Change: 2019-09-08 03:28:06.314537292 -0700
 Birth: -
```

Définir explicitement les acess et modification times : `touch -c -t YYDDHHMM filename`

Mettre à jour l'access et le modification time:`touch -c -d fileName`

Nous utilisons le timestamp d'un autre fichier avec l'option -r : `touch -r refrence_file_name file_name`

```
root@ubuntu16-1:~/test-space# stat file1
  File: 'file1'
  Size: 0             Blocks: 0          IO Block: 4096   regular empty file
Device: 801h/2049d    Inode: 2228281     Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2019-09-08 02:44:27.744736670 -0700
Modify: 2019-09-08 02:44:27.744736670 -0700
Change: 2019-09-08 02:44:27.744736670 -0700
 Birth: -
root@ubuntu16-1:~/test-space# touch -r file1 myfile 
root@ubuntu16-1:~/test-space# stat myfile 
  File: 'myfile'
  Size: 0             Blocks: 0          IO Block: 4096   regular empty file
Device: 801h/2049d    Inode: 2228284     Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2019-09-08 02:44:27.744736670 -0700
Modify: 2019-09-08 02:44:27.744736670 -0700
Change: 2019-09-08 03:34:14.724762374 -0700
 Birth: -
```

### mkdir

La commande mkdir dans Linux permet à l'utilisateur de créer des dossiers (répertoires)

```
mkdir [options...] [directories ...]
```

```
root@ubuntu16-1:~/test-space# mkdir dir1
root@ubuntu16-1:~/test-space# ls -l
total 4
drwxr-xr-x 2 root root 4096 Sep  8 03:44 dir1
```

Cette commande peut créer plusieurs dossiers d'un coup et aussi définir les permissions pour le dossier. Il est important de noer que l'utilisateur qui exécute cette commande doit avoir assez de permissions pour créer un dossier dans le dossier parent, ou il recevra une erreur  ‘permission denied’(accès refusé).

```
root@ubuntu16-1:~/test-space# mkdir --help
Usage: mkdir [OPTION]... DIRECTORY...
Create the DIRECTORY(ies), if they do not already exist.

Mandatory arguments to long options are mandatory for short options too.
  -m, --mode=MODE   set file mode (as in chmod), not a=rwx - umask
  -p, --parents     no error if existing, make parent directories as needed
  -v, --verbose     print a message for each created directory
  -Z                   set SELinux security context of each created directory
                         to the default type
      --context[=CTX]  like -Z, or if CTX is specified then set the SELinux
                         or SMACK security context to CTX
      --help     display this help and exit
      --version  output version information and exit

GNU coreutils online help: <http://www.gnu.org/software/coreutils/>
Full documentation at: <http://www.gnu.org/software/coreutils/mkdir>
or available locally via: info '(coreutils) mkdir invocation'
```

Essayons l'une des options les plus utiles, `-p` active la commande pour créer les dossiers parents si nécessaire. Si les dossiers existent aucune erreur n'est spécifiée :

```
root@ubuntu16-1:~/test-space# mkdir -p dir1/floor1/floor2
root@ubuntu16-1:~/test-space# tree
.
└── dir1
    └── floor1
        └── floor2

3 directories, 0 files
```

Nous pouvons créer un dossier et définir les permisssions (nous verrons cela plus tard) pour ce dossier en même temps en utilisant l'option -m :

```
root@ubuntu16-1:~/test-space# mkdir -m777 dir2 
root@ubuntu16-1:~/test-space# ls -l
total 8
drwxr-xr-x 3 root root 4096 Sep  8 03:57 dir1
drwxrwxrwx 2 root root 4096 Sep  8 04:09 dir2
```

La synaxe du mode est la même que la commande chmod (nous la verrons plus tard).

```
root@ubuntu16-1:~/test-space# mkdir -v -m o=--- dir3
mkdir: created directory 'dir3'
root@ubuntu16-1:~/test-space# ls -l
total 12
drwxr-xr-x 3 root root 4096 Sep  8 03:57 dir1
drwxrwxrwx 2 root root 4096 Sep  8 04:09 dir2
drwxr-x--- 2 root root 4096 Sep  8 04:14 dir3
```

Avec l'option `-v`option, il affiche un message pour chaque dossier créé.

### cp

La commande `cp` est un utilitaire en ligne de commande pour copier les fichiers et les dossiers.  \[Les copies des fichiers sont indépendants du fichier original (contrairement à la commande  `mv`)  ]. La syntaxe basique de la commande cp est :

```
cp [options….] source(s) destination
```

> cp peut prendre un ou plusieurs source mais ne peut prendre qu'une seule destination.

Il supporte la copie d'un ou plusieurs fichiers ou dossier avec les options pour faire des sauvegardes et préserver les attributs.

N'oubliez pas de considérer les types de la source et de la destination (fichiers ou dossiers) lorsque vous utilisez la commande cp.

* Si la cible est un dossier existant, alors toutes les sources sont copiés dans la cible. 
* Si la cible est un dossier qui n'existe pas, alrors la source (unique) doit aussi être un dossier et une copie du dossier source et de son contenu est faite avec le nom de la destination comme nouveau nom. 
* Si la cible est un fichier, alors la source (unique) doit aussi être un fichier et une copie du fichier source est faite avec le nom de la cible comme nouveau nom, en remplaçant tous fichier existant avec le même nom.

Quelques exemples :

```
root@ubuntu16-1:~/test-space# tree
.
├── dir1
│   └── floor1
│       └── floor2
├── dir2
├── dir3
└── file1

5 directories, 1 file
```

**Copier un fichier:**

```
root@ubuntu16-1:~/test-space# cp file1 file2
root@ubuntu16-1:~/test-space# ls
dir1  dir2  dir3  file1  file2
```

**Faire une sauvegarde lors de la copie d'un fichier**

Si une opération de copie écrase un fichier, le flag `-b` peut être utilisé pour créer une sauvegarde de ce fichier. Cela copie le fichier à la place et écrit un fichier de sauvegarde.

```
root@ubuntu16-1:~/test-space# ls
dir1  dir2  dir3   file1  file2  
root@ubuntu16-1:~/test-space# echo "this is file2 content" > file2
root@ubuntu16-1:~/test-space# echo "ehis is file1 content" > file1
root@ubuntu16-1:~/test-space# cp -b file1 file2
root@ubuntu16-1:~/test-space# ls
dir1  dir2  dir3   file1  file2  file2~ 
root@ubuntu16-1:~/test-space# cat file2
ehis is file1 content
root@ubuntu16-1:~/test-space# cat file2~
this is file2 content
```

Spécifiger le nom du fichier de sauvegarde en utilisant l'option `-S`. Essayez `cp -S .file2bak file1 file2`

**Copier plusieurs fichiers (dans un dossier)**

```
root@ubuntu16-1:~/test-space# cp file1 file2 dir2/
root@ubuntu16-1:~/test-space# tree
.
├── dir1
│   └── floor1
│       └── floor2
├── dir2
│   ├── file1
│   └── file2
├── dir3
├── file1
├── file2
└── file2~


5 directories, 4 files
```

**Copier les fichiers récursivement**

Nous pouvons utiliser cp pour copier des structure complète de dossier d'un endroit à un autre en utilisant l'option `-R `vpour faire une copie récursive.

Disons que vous êtes l'utilisateur root et que vous avez un dossier, /home/test-space, qui contient de nombreux fichiers et sous-dossiers. Vous voulez copier tous ces fichiers, et tous les sous-dossiers (et les fichiers et sous-dossiers qu'ils contiennent) dans un nouvel emplacement, /root/files-backup. Vous pouvez tous les copier en utilisant la commande :

`cp -R ~/test-space ~/files-backup`

* Si le dossier files-backup existe déjà, les fichiers du dossiers seront placés à l'intérieur.
* Si files-backup n'existe pas, il sera créé et le contenu des dossiers sera placé à l'intérieur.

**Copier un dossier**

Par défaut la commande cp ne copie pas les dossier. Essayer de copier un dossier donnera une erreur.

```
root@ubuntu16-1:~/test-space# cp dir2 dir4
cp: omitting directory 'dir2'
```

Pour copier un dossier passer l'option `-R`. Cela copiera de manière récursive un dossier et créera une copie.

```
root@ubuntu16-1:~/test-space# cp -R dir1 NewDirectoryA
root@ubuntu16-1:~/test-space# tree
.
├── dir1
│   └── floor1
│       └── floor2
├── dir2
│   ├── file1
│   └── file2
├── dir3
├── file1
├── file2
├── file2~
└── NewDirectoryA
    └── floor1
        └── floor2

8 directories, 5 files
```

**Copier plusieurs dossiers**

Pour copier plusieurs dossier donner le chemin des dossiers à copié suivi par le dossier de destination.

```
root@ubuntu16-1:~/test-space# cp -R dir1 dir2 NewDirectoryB
cp: target 'NewDirectoryB' is not a directory
root@ubuntu16-1:~/test-space# mkdir NewDirectoryB
root@ubuntu16-1:~/test-space# cp -R dir1 dir2 NewDirectoryB
root@ubuntu16-1:~/test-space# tree
.
├── dir1
│   └── floor1
│       └── floor2
├── dir2
│   ├── file1
│   └── file2
├── dir3
├── file1
├── file2
├── file2~
├── NewDirectoryA
│   └── floor1
│       └── floor2
└── NewDirectoryB
    ├── dir1
    │   └── floor1
    │       └── floor2
    └── dir2
        ├── file1
        └── file2

13 directories, 7 files
```

Regardons maintenant quelques options de la commande cp :

| cp command useful options: | Description                                                                                                                                                                                                                                        |
| -------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| -v,--verbose               | Verbose mode; explain what is being done.                                                                                                                                                                                                          |
| --preserve                 | preserve file attributes (permissions, group and user owernship).By default mode, ownership and timestamps will be preserved.                                                                                                                      |
| -i , --interactive         | Prompt before overwrite (overrides a previous -n option).                                                                                                                                                                                          |
| -n , --no-clobber          | Do not overwrite an existing file. If -i/--interactive was previously specified, this option overrides it. This option cannot be specified with -b/--backup, because backups are only created when a file would have been overwritten.             |
| -f,--force                 | If an existing destination file cannot be opened, remove it and try again. This option has no effect if the -n/--no-clobber option is used. However, it applies independently of -i/--interactive; neither option cancels the effect of the other. |

La commande cp a de nombreuses options. Essayez la commande `man cp` pour plus d'information.

### mv

`mv` est utilisé pour **déplacer ou renomer un ou plusieurs fichiers ou dossiers**. En générale, les noms peuvent être suivi par les même règle que la copie avec cp; \[Si vous déplacer un fichier sur le même système de fichier, l'inode ne changera pas]. 

* Renommer un fichier (ou un dossier) d'un nom source à une destination :

```
mv [options]  source destination
```

* Déplacer un fichier source ou un dossier dans un dossier nommé  destination:

```
mv [options] source [source2 ...] destination
```

Comme la syntaxe précédente, mais en spécifiant d'abord le dossier et le fichier source (ou dossier) en dernier

```
mv [options] -t destination source [source2 ...]
```
Essayons la commande mv :

```
root@ubuntu16-1:~/test-space# tree -F
.
├── dir1/
├── dir2/
├── file1
```

**Renomer un fichier ou un dossier:**

```
root@ubuntu16-1:~/test-space# mv file1 NewFile1
root@ubuntu16-1:~/test-space# mv dir1 dirA
root@ubuntu16-1:~/test-space# tree -F
.
├── dir2/
├── dirA/
├── file2
└── NewFile1
```

**Déplacer des fichiers dans un dossier :**

```
root@ubuntu16-1:~/test-space# mv NewFile1 file2 dir3
mv: target 'dir3' is not a directory
root@ubuntu16-1:~/test-space# mkdir dir3
root@ubuntu16-1:~/test-space# mv NewFile1 file2 dir3
root@ubuntu16-1:~/test-space# tree -F
.
├── dir2/
├── dir3/
│   ├── file2
│   └── NewFile1
└── dirA/

3 directories, 2 files
```

**Déplacer un dossier dans un autre :**

```
root@ubuntu16-1:~/test-space# mv dir3 dirA
root@ubuntu16-1:~/test-space# tree -F
.
├── dir2/
└── dirA/
    └── dir3/
        ├── file2
        └── NewFile1

3 directories, 2 files
```

Nous pouvons utiliser aussi la commande `mv -t dirA dir3`. `-t, --target-directory` Déplace toutes les sources dans le dossier de destination.

Options utiles de la commande mv:

| mv command options | description                                               |
| ------------------ | --------------------------------------------------------- |
| -v                 | verbose - print source and destination files              |
| -i                 | interactive prompt before overwrite                       |
| -u                 | update - move when source is newer than destination       |
| -f                 | force move by overwriting destination file without prompt |

> #### cp vs mv :
>
> Normalement, la commande cp copie un fichier par dessus une copie existante, si le fichier existant peut être écrit. D'un autre côté, la commmande mv ne déplacera ou ne renommera pas un fichier si la cible existe. Nous pouvons surcharger ce comportement avec l'option `-f`.

### rm

`rm` signifie **supprimer(remove)**. `rm` supprime les fichiers ou dossiers.

```
rm [OPTION]... FILE...
```

Essayons:

```
root@ubuntu16-1:~/test-space# tree -F
.
├── dir1/
├── dir2/
│   └── MyFile
└── file1

2 directories, 2 files
```

**Supprimer des fichiers**

```
root@ubuntu16-1:~/test-space# rm file1
root@ubuntu16-1:~/test-space# tree -F
.
├── dir1/
└── dir2/
    └── MyFile

2 directories, 1 file
```

{% hint style="info" %}
**Les fichiers dont le nom commencent par un tiret, comment les supprimer ?**

Pour supprimer un fichier dont le nom commence avec un tiret ("-"), vous pouvez spécifier un double tiret ("--") avant le nom du fichier. Le tiret supplémentaire est nécessaire pour que rm n'interprète pas mal le nom du fichier comme une option: `rm -- -file.txt` Ou, nous pouvons le supprimer en se référant au chemin : `rm /home/hope/-file.txt`
{% endhint %}

D'autres options de la commande rm :

| mv options | description                                                  |
| ---------- | ------------------------------------------------------------ |
| -v         | Verbose mode; explain at all times what is being done.       |
| -i         | prompt before every removal                                  |
| -f,--force | Ignore none existant files,and never prompt before removing. |
| -d,--dir   | Remove empty directories.                                    |

#### Supprimer des dossiers

```
root@ubuntu16-1:~/test-space# rm dir1
rm: cannot remove 'dir1': Is a directory
```

Par défaut, `rm` ne supprime pas les dossier.

Si le dossier spécifié est vide, il peut être supprimé avec l'option -d/--dir.

```
root@ubuntu16-1:~/test-space# rm -d dir1
root@ubuntu16-1:~/test-space# tree -F
.
└── dir2/
    └── MyFile

1 directory, 1 file
```

Qu'en est t'il si le dossier contient des fichiers ? Si l'option **-r/-R/--recursive** est spécifié, nous pouvons supprimer ce dossier cependant rm supprimera tout dossier qui correspond et leur contenu!

```
root@ubuntu16-1:~/test-space# rm -rf dir2
root@ubuntu16-1:~/test-space# tree -F
.

0 directories, 0 files
```

`rm -d` nous permet de supprimer un dossier sans spécifier **-r/-R/--recursive**, étant donné que le dossier est **vide**. En d'autres termes, **rm -d** est équivanlent à l'utilisation de **rmdir**.

### rmdir

Supprimer les dossiers en utilisant la commande rmdir est l'opposé de leur création. Nous pouvons supprimer un dossier avec `rmdir` que s'il est **vide** puisqu'il n'y **pas d'option pour forcer la suppresion**.

```
rmdir [options] directory ...
```

La encore, il y a une option `-p` qui supprime aussi les dossiers parents. Essayons :

```
root@ubuntu16-1:~/test-space# tree -F
.
├── dir1/
│   └── file1
├── dir2/
└── dir3/
    └── dirA/
        └── dirB/

5 directories, 1 file
root@ubuntu16-1:~/test-space# rmdir dir1
rmdir: failed to remove 'dir1': Directory not empty
root@ubuntu16-1:~/test-space# rmdir dir2
root@ubuntu16-1:~/test-space# tree -F
.
├── dir1/
│   └── file1
└── dir3/
    └── dirA/
        └── dirB/

4 directories, 1 file
root@ubuntu16-1:~/test-space# rmdir -p dir3/dirA/
root@ubuntu16-1:~/test-space# rmdir -p dir3/dirA/dirB/
root@ubuntu16-1:~/test-space# tree -F
.
└── dir1/
    └── file1

1 directory, 1 file
```

Normalement, lorsque `rmdir` est utilisé pour supprimé un dossier non-vide, il rapporte une erreur. L'option `--ignore-fail-on-non-empty` supprime ces messages d'erreur.

## Gérer plusieurs fichiers et dossiers

Parfois nous avons besoin de travailler sur plus d'un fichier. Nous allons maintenant étudier les commandes récursives.

### Manipulation Recursive 

**Liste récursive**

La commande ls a une option -R (noter le “R” en majuscule) pour lister un dossier et tous ses sous-dossiers. L'option récursive s'applique seulement sur le nom des dossier, par exemple essayons dans une arborescence.

```
root@ubuntu16-1:~/test-space# tree
.
├── dir1
│   └── text1
├── dir2
├── file1
└── file2

2 directories, 3 files
root@ubuntu16-1:~/test-space# ls -R
.:
dir1  dir2  file1  file2

./dir1:
text1

./dir2:
```

**Copie récursive**

Nous pouvons utiliser l'option -r (ou -R ou --recursive) pour demander à la commande cp de descendre dans les dossiers sources et de copier leurs contenu de manière récursive. Pour éviter une récursion infinie, nous ne pouvons copier le dossier source lui même!

```
root@ubuntu16-1:~# cp -R test-space NewCopy
root@ubuntu16-1:~# tree NewCopy/
NewCopy/
├── dir1
│   └── text1
├── dir2
├── file1
└── file2

2 directories, 3 files
```

**Suppression récursive**

Comme expliqué précédement _rmdir supprime seulement les dossiers **vide**_. Nous pouvons utiliser l'option `-r` (ou -R ou --recursive) pour demander à la commande rm de supprimer à la fois les fichiers et les dossiers :

```
root@ubuntu16-1:~# ls
client.conf             NewCopy      unzip_6.0-20ubuntu1_amd64.deb
files-backup            openvpn.key  zip_3.0-11_amd64.deb
jcal_0.4.1-2_amd64.deb  test-space
root@ubuntu16-1:~# rm -rf NewCopy/
root@ubuntu16-1:~# ls
client.conf             openvpn.key                    zip_3.0-11_amd64.deb
files-backup            test-space
jcal_0.4.1-2_amd64.deb  unzip_6.0-20ubuntu1_amd64.deb
```

## Jokers et Globbing

**Le globbing de fichier est une fonctionnalité fournie par le shell UNIX/Linux pour représenter plusieurs noms de fichier en utilisant un caractère spécifal appelé joker avec un seul nom de fichier.** Un joker est essentiellement un symbole qui peut être utiliser pour substituer un ou plusieurs caractères. De fait, nous pouvons utiliser les jokers pour générer la combinaison appropriés de nom de ficheirs comme nous avons besoin.

```
root@ubuntu16-1:~/test-space# ls -l
total 0
-rw-r--r-- 1 root root 0 Sep 28 00:26 aaa.txt
-rw-r--r-- 1 root root 0 Sep 28 00:27 ababa.txt
-rw-r--r-- 1 root root 0 Sep 28 00:26 bbbb.txt
-rw-r--r-- 1 root root 0 Sep 27 23:33 file1
-rw-r--r-- 1 root root 0 Sep 27 23:33 file2
```

*  **`?`** est utilisé pour correspondre à un seul caractère. Nous pouvons utiliser ‘?’ plusieurs fois pour correspondre à  plusieurs caractères.

```
root@ubuntu16-1:~/test-space# ls ???.txt
aaa.txt
```

* **`*`** est utilisé pour correspondre à 0 ou plus de caractères. Si nous avons peu d'information sur la recherche de n'importe quel fichier ou information nous pouvont utiliser ‘\*’ dans le modèle de globbing.

```
root@ubuntu16-1:~/test-space# ls *.txt
aaa.txt  ababa.txt  bbbb.txt
```

* **`[ ]`** est utilisé pour correspondre au caractère du rang. Les rangs les plus utilisés sont présentés ci-dessous :

`[A-Z]` : Toutes les lettres de l'alphabet en majuscule

`[a-z]` : Toutes les lettres de l'alphabet en miniscule

`[a-zA-Z0-9]` : Toutes les lettres de l'alphabet en majuscule, miniscule et les chiffres

> Le caractère `-` entre deux autres représente un rang qui inclu les deux autres caractères et tous les caractères entre les eux dans une séquence qui se suit.

```
root@ubuntu16-1:~/test-space# ls [e-z]*
file1  file2
```

* Le caractère **`!`** signifie ne correspond PAS à un caractère**.

```
root@ubuntu16-1:~/test-space# ls [!e-z]*
aaa.txt  ababa.txt  bbbb.txt
```

* **`{ }`** peut être utilisé pour faire correspondre des noms de fichier avec un ou plusieurs modèles de globbing. Chqaue modèle est séparé par  ‘,’ dans des accolades sans aucun espace.

```
root@ubuntu16-1:~/test-space# ls {???.txt,????.txt}
aaa.txt  bbbb.txt
```

`rm {*.doc,*.docx}` : supprime tous les ficheirs avec les extensions ‘doc’ ou ‘docx’.

* Et enfin **`\ `** est utilisé comme caractère d'échappement, nous l'utilisons poru protéger une séquence de caractère spéciaux. Exemple : "\\\” recherche un antislash

> Nous pouvons désactiver le globbing en utilisant la commande `set -f`.

{% hint style="danger" %}
**MOdèles de jokers vs Expressions régulières**

Modèles de jokers et expressions régulière partage certaines caractèristiques, mais il ne sont pas la même chose. Soyez attentif.
{% endhint %}

Maintenant que nous avons couvert le sujet des fichiers et des dossiers avec le marteau de la récursivité pour atteindre tout, et le marteau du globbing pour frapper de manière plus sélective, étudions la commande find, qui est plus comme un couteau suisse.

### Trouver des fichiers

La commande find est utiliser pour trouver des fichiers dans un ou plusieurs arborescence de dossiers, en utilisant des critères comme le nom, le timestamp ou la taille.

### find

La commande find recherche des fichiers ou dossiers en utilisant tout ou une partie du nom, ou en recherchant avec d'autre critères comme la taille, le type, le propriétaire du fichier, la date de création ou la date de dernière accès.

```
find starting/path options  expression
```

1. L'attribut `starting/path` définit le niveau le plus haut ou la recherche commence.
2. L'attribut `options` controle le comportement et les méthodes d'optimisation du processus de recherche.
3. L'attribut `expression` controle les tests que la recherche dans la hierarchie du dossier pour produire une sortie.

    La recherche la plus basique est de chercher par le nom ou une partie du nom :

```
root@ubuntu16-1:~/test-space# find /etc/ -iname "[y-z]*"
/etc/xdg/autostart/zeitgeist-datahub.desktop
/etc/zsh_command_not_found
/etc/vmware-tools/messages/zh_TW
/etc/vmware-tools/messages/zh_CN
/etc/dhcp/dhclient-exit-hooks.d/zzz_avahi-autoipd
/etc/kernel/postrm.d/zz-update-grub
/etc/kernel/postinst.d/zz-update-grub
/etc/brltty/Contraction/zu.ctb
/etc/brltty/Contraction/zh-tw-ucb.ctb
/etc/brltty/Contraction/zh-tw.ctb
```

L'option `-name` est utilisé pour rechercher des fichiers en se basant sur leur nom. `-i` le rend insensible à la casse. Dans le premier exemple, nous avons trouver les fichier et un dossier (/etc). 

{% hint style="info" %}
**trouver des fichiers cachés :** Si vous voulez trouver un fichier ou un dossier dont le nom commence par un point, comme .bashrc ou le dossier courant (.), alors vous devez spécifier un point comme faisant partie du modèle. Autrement, la recherche par nom ignore ces fichiers et dossiers. `find . -name ".*"`
{% endhint %}

note: Si vous voulez lier différents résultats ensemble, vous pouvez utiliser la commande “-and” ou “-or”. Le “-and” est assumé s'il est omis. `find . -name file1 -or -name file9`

**Trouver des fichiers par type**

Nous pouvons spécifier le type de ficheir que nous voulons trouver avec le paramètre “-type”. Par exemple :

```
find -type type_descriptor query
```

* `-type f` cherche un fichier régulier
* `-type d` cherche un dossier
* `-type l` cherche un lien symbolique

```
root@ubuntu16-1:~/test-space# find /etc/ -type f -iname "[y-z]*"
/etc/xdg/autostart/zeitgeist-datahub.desktop
/etc/zsh_command_not_found
/etc/dhcp/dhclient-exit-hooks.d/zzz_avahi-autoipd
/etc/kernel/postrm.d/zz-update-grub
/etc/kernel/postinst.d/zz-update-grub
/etc/brltty/Contraction/zu.ctb
/etc/brltty/Contraction/zh-tw-ucb.ctb
/etc/brltty/Contraction/zh-tw.ctb
```

**Trouver des fichiers par taille**

Nous pouvons aussi rechercher par taille de fichier, soit pour une taille spécifique (n) pour pour les fichier qui sont plus grand (+n) ou plus petit qu'une valeur donnée (-n). En utilisant les tailles supérieures et inférieure, nous pouvons trouver les fcihier dont la taille fait partie d'un intervalle. Par défaut l'option -size de find suppose que l'unité est ‘b’ pour les blocs de 512-byte.

* `-size  +/- [b] [c] [w] [k] [M} [G]`

```
b     pour les blocs de 512-byte (c'est la valeur par défaut si aucun suffixe n'est utilisé)
c     pour bytes
w     pour les mots two-byte
k     pour Kilobytes (unité de 1024 bytes)
M     pour Megabytes (unité de 1048576 bytes)
G     pour Gigabytes (unité de 1073741824 bytes)
```

Par exemple, chercherons les fichier plus petit que 1 kilobytes:

```
root@ubuntu16-1:~/test-space# find /etc/ -type f -size -1k
/etc/dictionaries-common/ispell-default
/etc/cups/printers.conf
/etc/vmware-caf/pme/config/_previous_/cafenv.config
/etc/.pwd.lock
/etc/apt/trusted.gpg.d/peek-developers_ubuntu_stable.gpg~
/etc/security/opasswd
/etc/gconf/gconf.xml.defaults/%gconf-tree.xml
/etc/gconf/gconf.xml.mandatory/%gconf-tree.xml
/etc/sensors.d/.placeholder
/etc/newt/palette.original
```

Nous pouvons trouver tous les fichiers vide en utilisant `find . -size 0b` ou `find . -empty` .

**Trouver les fichiers en fonction de leur date :**

Linux stocke les données de temps sur la date d'accès, de modification et de changement.

* **Access Time**: Dernière fois où le fichier a été lu ou écrit.
* **Modification Time**: Dernière fois où le contenu du fichier a été modifié.
* **Change Time**: Dernière fois ou les métadonnées du fichier ont été modifié.

Nous pouvons utiliser le timestamp decrit avec la commande touch pour localiser les fichiers qui ont un timestamp spécifique

```
-amin n: The file was last accessed n minutes ago
-anewer: The file was last accessed more recently than it was modified
-atime n: The file was last accessed more n days ago
-cmin n: The file was last changed n minutes ago
-cnewer: The file was last changed more recently than the file was modified
-ctime n: The file was last changed more than n days ago
-mmin n: File's data was last modified n minutes ago
-mtime n: File's data was last modified n days ago
```

De nouveau les signes (+/-) peuvnt être utilisé pour donnée un interval.

Trouver les fichier modifiés les deux dernières heures:

```
root@ubuntu16-1:~/test-space# find /home -cmin -120
/home/user1/.cache/upstart
/home/user1/.cache/upstart/indicator-printers.log
/home/user1/.cache/upstart/unity-panel-service.log
```

note : Ajouter l'option `-daystart` à `-mtime` ou `-atime` signifie que nous voulons considérer les jours comme des jours calendar, qui commence à minuit. Donc pour lister les fichiers régulier de notre dossier home qui ont été modifié hier nous pouvons utilisé `find ~/ -daystart -type f -mtime 1` .

> Nous pouvons aussi trouver les fichiers par propriétaires et permissions et utiliser les filtres sur les résultat par profondeur (nous verrons cela dans la section "104-7")

Deux autres options pour les fichiers :

| find command switch | meaning                                                 |
| ------------------- | ------------------------------------------------------- |
| -ls                 | list current file in ls -dils format on standard output |
| -print              | print the full file name on the standard output         |

Comme vous pouvez le foir la commande find a des tonne d'option, pour obtenir plus de détail utiliser la page de manuel de find.

#### Exécuter et conbiner des commandes Find (-exec)

Nous pouvons exécuter une commande sur n'importe quel résultat en utilisant le paramètre “-exec”. Cela s'utilise comme ça :

```
find find_parameters -exec command_and_params {} \;
```

Le `{}` est utilisé comme un jeton de remplacement pour les fichiers qui find trouve. Le “\\;” est utilisé pour que find sache où la commence termine.

Par exemple pour supprimer les fichiers vides :

```
find . -empty -exec rm {} \;
```

Nous pouvons supprimer tous les fichiers vide dans un dossier et ses sous-dossiers :

```
find . -empty -exec rm '{}' \;
```

Nous pouvons copier les fichiers du dossier comme cela :

```
find . -name "*.deb" -exec cp {} /tmp/packages \;
```

## Identifier les fichiers <a href="identifying-files" id="identifying-files"></a>

Les noms de fichier ont souvent un suffixe comme gif, jpeg, ou html qui nous indique ce que le fichier doit contenir. Linux n'a pas besoin de tel suffixes et ne les utilise généralement pas pour identifier le type d'un fichier. Connaitre le type de fichier avec lequel vous travailler vous aidera a savoir quel programme utiliser pour l'afficher ou le manipule.

### file

La commande file nous donne des information sur le type de donnée d'un ou plusieurs fichiers.

```
file [option] [filename]
```

```
root@ubuntu16-1:~/test-space# ls -l
total 10880
-rwxrw-rw- 1 user1 user1 10819475 Mar 29  2015 01 - Intro.mp4
-rw------- 1 user1 user1   221941 Sep  1 00:24 zabbix-cli-manual-1.7.0.pdf
-rw------- 1 user1 user1    91087 Aug 31 00:40 zabbix-cli-rpm-2.1.1-1.tar.gz

root@ubuntu16-1:~/test-space# file 01\ -\ Intro.mp4 
01 - Intro.mp4: ISO Media, MP4 Base Media v1 [IS0 14496-12:2003]
```

`-b, –brief`:  Est utilisé pour afficher seulement le type de fichier en mode court.

```
root@ubuntu16-1:~/test-space# file  -b zabbix-cli-manual-1.7.0.pdf 
PDF document, version 1.4
```

`-z` : Regarde ce qu'il y a dans un fichier compressé.

```
root@ubuntu16-1:~/test-space# file -z zabbix-cli-rpm-2.1.1-1.tar.gz zabbix-cli-rpm-2.1.1-1.tar.gz:
POSIX tar archive (gzip compressed data, from Unix)
```

Et l'option `-i` : Permet de voir le type mime du fichier.

{% hint style="success" %}
Un type MIME est une étiquette utilisé pour identifié le type de données. Il est utilisé pour que logiciel sache comme gérer les données. Il sert à la même cchose sur internet sur les extensions de fichier font sur Microsoft Windows.
{% endhint %}

### Archiver et compresser des fichiers

Archiver et compresser des fichiers sont deux choses différentes :

![](.gitbook/assets/performbasicfile-archcompress.jpg)

* tar archive seulement les fichier et ne fait pas de compression par défaut.
* zip fait à la fois l'archivage et la compression.
* gzip et bzip2 sont utilisé seulement pour la compression.

![](.gitbook/assets/performbasicfile-compratio.jpg)

### zip

`Zip` est une des format d'archive de fichier les plus populaire. Avec zip, vous pouvez compresser plusieurs fichiers dans un seul fichier. Cela ne permet pas seulement d'économiser de l'espace, mais aussi d'économiser de la bande passante. C'est pourquoi vous trouver presque tout le temps des fichiers zip.

```
zip options archive inpath inpath ...
```

Essayons :

```
root@ubuntu16-1:~/test-space/myfiles# tree -F
.
├── dir1/
│   └── myvideo.mp4*
├── dir2/
│   ├── zabbix-cli-manual-1.7.0.pdf
│   └── zabbix-cli-rpm-2.1.1-1.tar.gz
├── myconf.txt
└── mylog.txt

2 directories, 5 files

root@ubuntu16-1:~/test-space/myfiles# zip myconf.zip myconf.txt 
  adding: myconf.txt (deflated 60%)

root@ubuntu16-1:~/test-space/myfiles# tree -F
.
├── dir1/
│   └── myvideo.mp4*
├── dir2/
│   ├── zabbix-cli-manual-1.7.0.pdf
│   └── zabbix-cli-rpm-2.1.1-1.tar.gz
├── myconf.txt
├── myconf.zip
└── mylog.txt

2 directories, 6 files
```

Utiliser l'option `-r` avec la commande zip et cela zipera les fichiers de manière récursive dans un dossier. Cette option peut vous aider à zipper tous les fichiers présent dans un dossier spécifique :

```
root@ubuntu16-1:~/test-space/myfiles# cd ..
root@ubuntu16-1:~/test-space# zip -r myfiles.zip myfiles/
  adding: myfiles/ (stored 0%)
  adding: myfiles/dir1/ (stored 0%)
  adding: myfiles/dir1/myvideo.mp4 (deflated 71%)
  adding: myfiles/etc/ (stored 0%)
  adding: myfiles/dir2/ (stored 0%)
  adding: myfiles/dir2/zabbix-cli-rpm-2.1.1-1.tar.gz (deflated 0%)
  adding: myfiles/dir2/zabbix-cli-manual-1.7.0.pdf (deflated 88%)
  adding: myfiles/mylog.txt (deflated 89%)
  adding: myfiles/myconf.txt (deflated 60%)
  adding: myfiles/myconf.zip (stored 0%)
```

### unzip

Un autre programme companion, unzip, extrait et décompresse les archives zip.

```
root@ubuntu16-1:~/test-space# rm -rf myfiles
root@ubuntu16-1:~/test-space# unzip myfiles.zip 
Archive:  myfiles.zip
   creating: myfiles/
   creating: myfiles/dir1/
  inflating: myfiles/dir1/myvideo.mp4  
   creating: myfiles/etc/
   creating: myfiles/dir2/
  inflating: myfiles/dir2/zabbix-cli-rpm-2.1.1-1.tar.gz  
  inflating: myfiles/dir2/zabbix-cli-manual-1.7.0.pdf  
  inflating: myfiles/mylog.txt       
  inflating: myfiles/myconf.txt      
 extracting: myfiles/myconf.zip   
```

| zip command example                     | Description                                     |
| --------------------------------------- | ----------------------------------------------- |
| zip –v filename.zip file1.txt           |  Verbose mode or print diagnostic version info. |
| zip –d filename.zip file.txt            |  Removes the file from the zip archive.         |
| zip –u filename.zip file.txt            | Updates the file in the zip archive.            |
| zip –m filename.zip file.txt            | Deletes the original files after zipping.       |
| zip –x filename.zip file_to_be_excluded | Exclude the files in creating the zip.          |

### Compresser les fichiers

### gzip

Gzip (GNU zip) est un outil de compression, qui est utiliser pour réduire la taile d'un fichier. 

```
gzip [OPTION]... [FILE]...
```

**Par défaut** le fichier original sera remplacé par le fichier compressé avec l'extension (.gz) et gzip supprime le fichier original après la création du fichier compressé. gzip garde le mode original du fichier, son propriétaire et son timestamp.

```
root@ubuntu16-1:~/test-space/myfiles# tree -F
.
├── dir1/
│   └── myvideo.mp4*
├── dir2/
│   ├── zabbix-cli-manual-1.7.0.pdf
│   └── zabbix-cli-rpm-2.1.1-1.tar.gz
├── myconf.txt
└── mylog.txt

2 directories, 5 files
root@ubuntu16-1:~/test-space/myfiles# gzip mylog.txt 
root@ubuntu16-1:~/test-space/myfiles# tree -F
.
├── dir1/
│   └── myvideo.mp4*
├── dir2/
│   ├── zabbix-cli-manual-1.7.0.pdf
│   └── zabbix-cli-rpm-2.1.1-1.tar.gz
├── myconf.txt
└── mylog.txt.gz

2 directories, 5 files
```

### gunzip

Pour décompresser un fichier nous pouvons utiliser la commande gunzip et votre fichier original sera de retour.

```
root@ubuntu16-1:~/test-space/myfiles# ls -l
total 136
drwxr-xr-x 2 root root   4096 Sep 30 05:42 dir1
drwxr-xr-x 2 root root   4096 Oct  5 05:25 dir2
-rw-r--r-- 1 root root   2084 Sep 30 05:44 myconf.txt
-rw-r----- 1 root root 123925 Sep 30 05:41 mylog.txt.gz
root@ubuntu16-1:~/test-space/myfiles# gunzip mylog.txt.gz 
root@ubuntu16-1:~/test-space/myfiles# ls -l
total 1144
drwxr-xr-x 2 root root    4096 Sep 30 05:42 dir1
drwxr-xr-x 2 root root    4096 Oct  5 05:25 dir2
-rw-r--r-- 1 root root    2084 Sep 30 05:44 myconf.txt
-rw-r----- 1 root root 1156369 Sep 30 05:41 mylog.txt
```

Nous pouvons utiliser l'option -d pour décompreser uun fichier en utilisant la commande “gzip”. ex : `gzip -d mydoc.gz`

| gzip command example | Description                                                                                                                                                                    |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| gzip -r testfolder   | compress every file in a folder and its subfolders                                                                                                                             |
| gzip -k mydoc.txt    | compress the file and keep the original file                                                                                                                                   |
| gzip -f myfile1.txt  | fore to compress already compressed file                                                                                                                                       |
| gzip -v mydoc.txt    | displays the name and percentage reduction for each file compressed or decompressed. we can use -l with compressed files.                                                      |
| gzip -L filename.gz  | displays the gzip license                                                                                                                                                      |
| gzip -9 mydoc.txt    | <p>-[1-9] option : It allows to change the compression level.</p><p>1 : fastest compression speed but lower ratio</p><p>9 : highest compression ratio but at a lower speed</p> |

### bzip2

Comme gzip, la commande bzip2 dans Linux est utilisé pour compressé et décompressé les fichiers. Il utilise différents algorythmes de compression donc il compresse les fichiers mieux que gzip, mais il a un temps de décompressions plus lent et une utilisation plus intensive de la mémoire.

```
bzip2 [OPTIONS] filenames ...
```

```
root@ubuntu16-1:~/test-space/myfiles# tree -F
.
├── dir1/
│   └── myvideo.mp4*
├── dir2/
│   ├── zabbix-cli-manual-1.7.0.pdf
│   └── zabbix-cli-rpm-2.1.1-1.tar.gz
├── myconf.txt
└── mylog.txt

2 directories, 5 files
root@ubuntu16-1:~/test-space/myfiles# bzip2 mylog.txt 
root@ubuntu16-1:~/test-space/myfiles# tree -F
.
├── dir1/
│   └── myvideo.mp4*
├── dir2/
│   ├── zabbix-cli-manual-1.7.0.pdf
│   └── zabbix-cli-rpm-2.1.1-1.tar.gz
├── myconf.txt
└── mylog.txt.bz2

2 directories, 5 files
```

### bunzip2

bunzip2 est utilisé pour la décompression des fichiers bzip2 :

```
root@ubuntu16-1:~/test-space/myfiles# ls -l
total 92
drwxr-xr-x 2 root root  4096 Sep 30 05:42 dir1
drwxr-xr-x 2 root root  4096 Oct  5 05:25 dir2
-rw-r--r-- 1 root root  2084 Sep 30 05:44 myconf.txt
-rw-r----- 1 root root 79421 Sep 30 05:41 mylog.txt.bz2
root@ubuntu16-1:~/test-space/myfiles# bunzip2 mylog.txt.bz2 
root@ubuntu16-1:~/test-space/myfiles# ls -l
total 1144
drwxr-xr-x 2 root root    4096 Sep 30 05:42 dir1
drwxr-xr-x 2 root root    4096 Oct  5 05:25 dir2
-rw-r--r-- 1 root root    2084 Sep 30 05:44 myconf.txt
-rw-r----- 1 root root 1156369 Sep 30 05:41 mylog.txt
```

L'option **-d** est aussi utilisé pour décompresser les fichiers compressés.

| bzip2 command example | Description                                                    |
| --------------------- | -------------------------------------------------------------- |
| bzip2 -k input.txt    | does compression but does not deletes the original file        |
| bzip2 -z input.txt    | forces compression. It is an opposite command of decompression |
| bzip2 -v input.txt    | show the compression ratio for each file processed             |
| bzip2 -L filename.gz  | -V , -L used to display the software version, license terms    |
| bzip2 -q input.txt    |  It will suppress non-essential warning messages.              |

bzip2 n'a pas d'option pour décompresser un dossier, donc nous utilisons tar pour cela. Comment? voir la section tar.

### Archiver des fichiers

Qu'est un fichier archive ?

Un fichier archive est un fichier qui est composé d'un ou plusieurs fichiers ainsi que leur métadonnées. Les fichiers archive sont utilisé pour rassembler plusieurs fichiers de données ensemble dans un seul fichier pour une portabilité plus grande et pour stocker, ou simplement pour compresser des fichiers et utiliser moins d'espace disque.

### tar

La commande Linux ‘tar’ signifie tape archive, est utilisé pour créer des archive et extraire les fichiers d'une archive. La commande tar dans Linux est une des commandes les plus important qui fournit des fonctionnalités d'archivage dans Linux.

Voici quelque cas d'utilisation communs de la commande tar :

* Sauvegarde des serveurs et bureau.
* Archivage de Document.
* Distribution de logiciel.

![](.gitbook/assets/performbasicfile-tar.jpg)

Commençons :

```
root@ubuntu16-1:~/test-space# tree -F
.
└── myfiles/
    ├── dir1/
    │   └── myvideo.mp4*
    ├── dir2/
    │   ├── zabbix-cli-manual-1.7.0.pdf
    │   └── zabbix-cli-rpm-2.1.1-1.tar.gz
    ├── myconf.txt
    └── mylog.txt

3 directories, 5 files
```

**Créer un fichier d'archive tar**

```
root@ubuntu16-1:~/test-space# tar -cvf myarch.tar myfiles
myfiles/
myfiles/dir1/
myfiles/dir1/myvideo.mp4
myfiles/dir2/
myfiles/dir2/zabbix-cli-rpm-2.1.1-1.tar.gz
myfiles/dir2/zabbix-cli-manual-1.7.0.pdf
myfiles/mylog.txt
myfiles/myconf.txt

root@ubuntu16-1:~/test-space# ls
myarch.tar  myfiles
```

Nous pouvons aussi inclure plus qu'un dossier, mais aussi d'en exclure avec l'option ` --exclude  `.

note: Par défaut tar décompresse les fichier dans le dossier courant et cela peut poser des problèmes (écrasement,...), pour éviter ça utiliser la commande `tar -xvf backupfile  -C  /restoreDir`.`  -C  ` signifie change le dossier avant d'extraire la sauvegarde.

**Untar tar Archive File**

```
root@ubuntu16-1:~/test-space# tar -xvf myarch.tar 
myfiles/
myfiles/dir1/
myfiles/dir1/myvideo.mp4
myfiles/dir2/
myfiles/dir2/zabbix-cli-rpm-2.1.1-1.tar.gz
myfiles/dir2/zabbix-cli-manual-1.7.0.pdf
myfiles/mylog.txt
myfiles/myconf.txt
```

Nous pouvons utiliser la commande Linux tar pour créer des archives compressé ou les décompressé et aussi les maintenir et les modifier.

![](.gitbook/assets/performbasicfile-targzipbzip2.jpg)

```
root@ubuntu16-1:~/test-space# tar -cvzf myfiles.tar.gz myfiles/
myfiles/
myfiles/dir1/
myfiles/dir1/myvideo.mp4
myfiles/dir2/
myfiles/dir2/zabbix-cli-rpm-2.1.1-1.tar.gz
myfiles/dir2/zabbix-cli-manual-1.7.0.pdf
myfiles/mylog.txt
myfiles/myconf.txt

root@ubuntu16-1:~/test-space# tar -cvjf myfiles.tar.bz2 myfiles/
myfiles/
myfiles/dir1/
myfiles/dir1/myvideo.mp4
myfiles/dir2/
myfiles/dir2/zabbix-cli-rpm-2.1.1-1.tar.gz
myfiles/dir2/zabbix-cli-manual-1.7.0.pdf
myfiles/mylog.txt
myfiles/myconf.txt

root@ubuntu16-1:~/test-space# ls -lh
total 6.4M
drwxr-xr-x 4 root root 4.0K Oct  5 23:51 myfiles
-rw-r--r-- 1 root root 3.2M Oct  6 01:59 myfiles.tar.bz2
-rw-r--r-- 1 root root 3.3M Oct  6 01:58 myfiles.tar.gz
```

Pour décompresser une arcchive au format tar.gzip utiliser  ` tar -xzvf file.tar.gz`  et `tar -xjvf file.tar.bz2` pour les fichiers bzip2.

```
tar command Options:
-c : Creates Archive
-x : Extract the archive
-f : creates archive with given filename
-v : Displays Verbose Information
-t : displays or lists files in archived file
-u : archives and adds to an existing archive file
-A : Concatenates the archive files
-z : zip, tells tar command that create tar file using gzip
-j : filter archive tar file using tbzip
-W : Verify a archive file
-r : update or add file or directory in already existed .tar file
wildcards – Specify patterns in unix tar command
```

note: L'option `-r` ne peut ajouter aucun fichier à un **fichier compressé**.  

Nous utilisons habituellement un mélange d'option de tar pour obtenir ce que nous voulons :

| tar command example                   | Description                                                |
| ------------------------------------- | ---------------------------------------------------------- |
| tar tf file.tar                       | list the contents and specify the tarfile                  |
| tar tvf file.tar                      | Viewing the Archive                                        |
| tar tvf file.tar filename             | We can pass a file name as an argument to search a tarfile |
| tar tvf file.tar --wildcards '\*.png' | Using wildcards with tar                                   |
| tar -tvfW file.tar                    | Verify tar  Archive File                                   |
| tar -rvf file.tar newfile.txt         | Add a file to .tar File                                    |
| tar -uvf file.tar newdir              | Add a directory to an existing .tar file                   |

![](.gitbook/assets/performbasicfile-over.jpg)


### dd

dd permet de Convertir et Copier mais pourquoi il ne s'appelle pas cc? Car le nom cc est déjà utilisé par le compileur c.  De nombreuses personnes l'appelle Disk Destroyer car dd ne so'ccupe pas du tout du système de fichier et travaille directement avec les blocs du périphériques!

```
dd [OPERAND]...
dd OPTION
```

 La synaxe de la ligne de commande dd diffère de beaucoup d'autre programmes Unix, en fait il utilise la syntaxe _option=value_ pour ses options de ligne de commande, plutôt que le format plus-standard _-option value_ ou _–option=value_. Par défaut, dd lit depuis stdin et écrit sur stdout, mais cela peut changer en utilisant les options if (input file) et  of (output file).

![](.gitbook/assets/performbasicfile-dd.jpg)

dd prend un fichier en entrée (ex:/dev/sda) et il écrit la sortie dans le fichier (ex:/dev/sdb) que nous avons spécifier, bs est la taille de bloc, comment nous voulons écrire de gros blocs et si ce n'est pas nécessaire peut être omis.

 Pour sauvegarder le disque dur en entier :

```
dd if = /dev/sda of = /dev/sdb
```

Nous pouvons même utiliser dd pour copier tout type de blocs de pérphériques et comme dd travaille sur les blocs de périphériques eux même il n'est pas important de savoir quels sont les partitions et si elles sont démarrés.

* S'il y a des erreurs, la commande ci-dessous échouera. Si vous lui donner le paramètre _“conv=noerror”_  alors il continuera à copier même si des erreurs sont lues.`dd if = /dev/sda of = /dev/sdb conv=noerror`
* L'entrée et la sortie devraient être mentionné avec beaucoup de précaution. Dans le cas où vous mentionner le périphérique source dans la cible et vice versa, vous pourriez perdre toutes les données.
* Pour copier, de disque dur à disque dur, utiliser la commande dd donnée ci-dessous, l'option sync permet de copier tout en utilisant la synchronisation des I/O.`dd if = /dev/sda of = /dev/sdb conv=noerror, sync`

| dd comand examples                                 | Description                                            |
| -------------------------------------------------- | ------------------------------------------------------ |
| dd if = /dev/sda of = /dev/sdb                     | backup entire Disk                                     |
| dd if=/dev/hda1 of=\~/partition.img                | backup a partition                                     |
| dd if = /dev/hda of = \~/hdadisk.img               | create an image of a Hard Disk                         |
| dd if = hdadisk.img of = /dev/hdb                  | restore using  Hard Disk image                         |
| dd if = /dev/cdrom of = tgsservice.iso bs = 2048   | create CD-Rom backup                                   |
| dd if=/path/to/ubuntu.iso of=/dev/sdb bs=1M        | create bootable usb drive from  image                  |
| dd if=textfile.ebcdic of=textfile.ascii conv=ascii | Convert the data format of a file from EBCDIC to ASCII |

conv peut faire de nombreuses choses comme convertir un fichier en majuscule et  visa versa.

### cpio

`cpio` signifie  “**C**o**p**y **i**n, copy **o**ut“. Il est uilisé pour traiter les fichiers archives comme  _.cpio ou _.tar. Cette comande peut copier des fichiers dans et depuis les archives.

* **Copy-out Mode:** Copier les fichiers nommés dans la liste des nom sur l'archive` command | cpio -o  > archive`
* **Copy-in Mode:** Extraire les fichiers depuis l'archive` cpio -i < archive`
* **Copy-pass Mode:** Copier les fichiers nommés dans la liste des noms vers le dossier de destination` cpio -p destination-directory < name-list`

![](.gitbook/assets/performbasicfile-cpio.jpg)

```
Policy options:
    -i, –extract: Extract files from an archive and it runs only in copy-in mode.
    -o, –create: Create the archive and it runs only in copy-out mode.
    -p, –pass-through: Run in copy-pass mode.
    -t, –list: Print a table of contents of all the inputs present.
```

| exemple de commande cpio       | Description                           |
| ------------------------------ | ------------------------------------- |
| ls /etc \| cpio -ov file.cpio  | Créer un fichier cpio                 |
| cpio -iv <  file.cpio          | extraire un fichier cpio              |
| ls \| cpio -ov -H tar file.tar | Créer un fichier tar en utilisant cpio|
| cpio -iv -F file.tar           | Extraire un fichier tar en utilisant cpio  |
| cpio -it -F < file.tar         | Voir seulement les fichiers d'une archive tar en utilisant cpio |

## Exercices

### Exercices guidés

* Considérez l’affichage ci-dessous :
  ```
    $ ls -lh
    total 60K
    drwxr-xr-x  2   frank   frank   4.0K    Apr 1   2018    Desktop
    drwxr-xr-x  2   frank   frank   4.0K    Apr 1   2018    Documents
    drwxr-xr-x  2   frank   frank   4.0K    Apr 1   2018    Downloads
    -rw-r--r--  1   frank   frank     21    Sep 7   12:59   emp_name
    -rw-r--r--  1   frank   frank     20    Sep 7   13:03   emp_salary
    -rw-r--r--  1   frank   frank   8.8K    Apr 1   2018    examples.desktop
    -rw-r--r--  1   frank   frank     10    Sep 1   2018    file1
    -rw-r--r--  1   frank   frank     10    Sep 1   2018    file2
    drwxr-xr-x  2   frank   frank   4.0K    Apr 1   2018    Music
    drwxr-xr-x  2   frank   frank   4.0K    Apr 1   2018    Pictures
    drwxr-xr-x  2   frank   frank   4.0K    Apr 1   2018    Public
    drwxr-xr-x  2   frank   frank   4.0K    Apr 1   2018    Templates
    drwxr-xr-x  2   frank   frank   4.0K    Apr 1   2018    Videos
  ```
  * Que représente le caractère d dans les résultats ?
<details>
  <summary>Réponse</summary>
        d est le caractère qui identifie un répertoire.
</details>
        
  * Pourquoi les tailles s’affichent-elles dans un format humainement lisible ?
<details>
  <summary>Réponse</summary>
        À cause de l’option -h.
</details>

* Qu’est-ce qui changerait dans l’affichage si ls était utilisé sans argument ?
<details>
  <summary>Réponse</summary>
        Seuls les noms des répertoires et des fichiers seraient indiqués.
</details>

* Considérez la commande ci-dessous :
  ```
    $ cp /home/frank/emp_name /home/frank/backup
  ```
  * Que se passera-t-il avec le fichier emp_name si cette commande est exécutée avec succès ?
<details>
  <summary>Réponse</summary>
        emp_name sera copié dans backup.
</details>

  * Si emp_name était un répertoire, quelle option devrait être ajoutée à cp pour exécuter la commande ?
<details>
  <summary>Réponse</summary>
        -r
</details>

  * Si cp est remplacé par mv, à quoi pouvez-vous vous attendre ?
<details>
  <summary>Réponse</summary>
        emp_name serait déplacé dans backup. Il n’existera plus dans le répertoire personnel de l’utilisateur frank.
</details>

* Considérez la liste de fichiers suivante :
  ```
    $ ls
    file1.txt file2.txt file3.txt file4.txt
  ```
  * Quel caractère de substitution permettrait de supprimer tout le contenu de ce répertoire ?
<details>
  <summary>Réponse</summary>
    L’astérisque *.
</details>

* En partant du listing précédent, quels fichiers seraient affichés par la commande suivante ?

    $ ls file*.txt
<details>
  <summary>Réponse</summary>
    Tous, puisque le caractère astérisque représente un nombre quelconque de caractères.
</details>

* Complétez la commande en ajoutant les chiffres et les caractères appropriés entre les crochets pour énumérer tout le contenu ci-dessus :

    $ ls file[].txt
<details>
  <summary>Réponse</summary>
    file[0-9].txt
</details>

* Considérez le listing suivant :
```
  $ find /home/frank/Documents/ -type d
  /home/frank/Documents/
  /home/frank/Documents/animal
  /home/frank/Documents/animal/domestic
  /home/frank/Documents/animal/wild
```
  * Quel genre de fichiers cette commande afficherait-elle ?
  <details>
  <summary>Réponse</summary>
    Des répertoires
  </details>

  * Dans quel répertoire la recherche doit-elle débuter ?
<details>
  <summary>Réponse</summary>
    /home/frank/Documents
  </details>

* Un utilisateur veut compresser son répertoire de sauvegarde. Il utilise la commande suivante :
```
$ tar cvf /home/frank/backup.tar.gz /home/frank/dir1
```
Quelle est l’option qui manque pour compresser la sauvegarde en utilisant l’algorithme gzip ?
<details>
  <summary>Réponse</summary>
  L’option -z.
</details>

### Exercices d’approfondissement

* Dans votre répertoire utilisateur, créez deux fichiers dog et cat.
<details>
  <summary>Réponse</summary>
    $ touch dog cat
</details>

* Toujours dans votre répertoire utilisateur, créez le répertoire animal. Déplacez dog et cat vers animal.
<details>
  <summary>Réponse</summary>
    $ mkdir animal
    $ mv dog cat -t animal/
</details>

* Allez dans le dossier Documents qui se trouve dans votre répertoire utilisateur et créez le répertoire backup à l’intérieur de celui-ci.
<details>
  <summary>Réponse</summary>
    $ cd ~/Documents
    $ mkdir backup
</details>

* Copiez animal et tout son contenu vers backup.
<details>
  <summary>Réponse</summary>
    $ cp -r animal ~/Documents/backup
</details>

* Renommez animal dans backup en animal.bkup.
<details>
  <summary>Réponse</summary>
  $ mv animal/ animal.bkup
</details>

* Le répertoire /home/lpi/databases contient de nombreux fichiers dont : db-1.tar.gz, db-2.tar.gz et db-3.tar.gz. Quelle commande simple pouvez-vous utiliser pour afficher uniquement les fichiers en question ?
<details>
  <summary>Réponse</summary>
    $ ls db-[1-3].tar.gz
</details>

* Considérez le listing suivant :

    $ ls
    cne1222223.pdf cne12349.txt cne1234.pdf

    En utilisant un seul caractère de substitution, quelle commande permettrait de supprimer uniquement les fichiers .pdf ?
<details>
  <summary>Réponse</summary>
  ```
    $ rm *.pdf
  ```
</details>

* Un administrateur système doit effectuer des vérifications régulières dans le but de nettoyer les fichiers volumineux. Ces fichiers volumineux se trouvent dans /var et se terminent par l’extension .backup.
  * Écrivez la commande basée sur find pour localiser ces fichiers :
<details>
  <summary>Réponse</summary>
        $ find /var -name *.backup
</details>

  * Une analyse de la taille de ces fichiers révèle qu’ils vont de 100M à 1G. Complétez la commande ci-dessus avec cette nouvelle information afin de pouvoir localiser les fichiers de sauvegarde allant de 100M à 1G :
<details>
  <summary>Réponse</summary>
        $ find /var -name *.backup -size +100M -size -1G
</details>

* Enfin, complétez la commande avec une action de suppression pour que ces fichiers soient effacés :
<details>
  <summary>Réponse</summary>
        $ find /var -name *.backup -size +100M -size -1G -delete
</details>

* Le répertoire /var contient quatre fichiers de sauvegarde :
```
    db-jan-2018.backup
    db-feb-2018.backup
    db-march-2018.backup
    db-apr-2018.backup
```
  * En utilisant tar, spécifiez la commande qui créerait un fichier d’archive nommé db-first-quarter-2018.backup.tar :
<details>
  <summary>Réponse</summary>
        $ tar -cvf db-first-quarter-2018.backup.tar db-jan-2018.backup db-feb-2018.backup db-march-2018.backup db-apr-2018.backup
</details>

  * En utilisant tar, spécifiez la commande qui va créer l’archive et la compresser en utilisant gzip. Notez que le nom du fichier résultant devra se terminer par .gz :
<details>
  <summary>Réponse</summary>
        $ tar -zcvf db-first-quarter-2018.backup.tar.gz db-jan-2018.backup db-feb-2018.backup db-march-2018.backup db-apr-2018.backup
</details>


.

.

.

[https://www.computerhope.com/unix/uls.htm](https://www.computerhope.com/unix/uls.htm)

[https://linuxize.com/post/how-to-list-files-in-linux-using-the-ls-command/](https://linuxize.com/post/how-to-list-files-in-linux-using-the-ls-command/)

[https://www.rapidtables.com/code/linux/ls.html](https://www.rapidtables.com/code/linux/ls.html)

[https://www.tecmint.com/8-pratical-examples-of-linux-touch-command/](https://www.tecmint.com/8-pratical-examples-of-linux-touch-command/)

[https://www.geeksforgeeks.org/touch-command-in-linux-with-examples/](https://www.geeksforgeeks.org/touch-command-in-linux-with-examples/)

[https://linuxize.com/post/linux-touch-command/](https://linuxize.com/post/linux-touch-command/)

[https://www.geeksforgeeks.org/mkdir-command-in-linux-with-examples/](https://www.geeksforgeeks.org/mkdir-command-in-linux-with-examples/)

[https://www.lifewire.com/create-directories-linux-mkdir-command-3991847](https://www.lifewire.com/create-directories-linux-mkdir-command-3991847)

[https://developer.ibm.com/tutorials/l-lpic1-103-3/](https://developer.ibm.com/tutorials/l-lpic1-103-3/)

[https://shapeshed.com/unix-cp/](https://shapeshed.com/unix-cp/)

[https://www.rapidtables.com/code/linux/cp.html](https://www.rapidtables.com/code/linux/cp.html)

[https://www.computerhope.com/unix/ucp.htm](https://www.computerhope.com/unix/ucp.htm)

[https://www.rapidtables.com/code/linux/mv.html](https://www.rapidtables.com/code/linux/mv.html)

[https://www.computerhope.com/unix/umv.htm](https://www.computerhope.com/unix/umv.htm)

[https://jadi.gitbooks.io/lpic1/content/1033\_perform_basic_file_management.html](https://jadi.gitbooks.io/lpic1/content/1033\_perform_basic_file_management.html)

[https://www.computerhope.com/unix/urm.htm](https://www.computerhope.com/unix/urm.htm)

[https://www.computerhope.com/unix/urmdir.htm](https://www.computerhope.com/unix/urmdir.htm)

[https://www.linuxnix.com/10-file-globbing-examples-linux-unix/](https://www.linuxnix.com/10-file-globbing-examples-linux-unix/)

[https://linuxhint.com/bash_globbing_tutorial/](https://linuxhint.com/bash_globbing_tutorial/)

[https://www.linode.com/docs/tools-reference/tools/find-files-in-linux-using-the-command-line/](https://www.linode.com/docs/tools-reference/tools/find-files-in-linux-using-the-command-line/)

[https://www.lifewire.com/uses-of-linux-command-find-2201100](https://www.lifewire.com/uses-of-linux-command-find-2201100)

[https://www.digitalocean.com/community/tutorials/how-to-use-find-and-locate-to-search-for-files-on-a-linux-vps](https://www.digitalocean.com/community/tutorials/how-to-use-find-and-locate-to-search-for-files-on-a-linux-vps)

[https://www.geeksforgeeks.org/tar-command-linux-examples/](https://www.geeksforgeeks.org/tar-command-linux-examples/)

[https://itsfoss.com/linux-zip-folder/](https://itsfoss.com/linux-zip-folder/)

[https://www.geeksforgeeks.org/zip-command-in-linux-with-examples/](https://www.geeksforgeeks.org/zip-command-in-linux-with-examples/)

[https://www.geeksforgeeks.org/gzip-command-linux/](https://www.geeksforgeeks.org/gzip-command-linux/)

[https://www.geeksforgeeks.org/bzip2-command-in-linux-with-examples/](https://www.geeksforgeeks.org/bzip2-command-in-linux-with-examples/)

[https://www.tecmint.com/xz-command-examples-in-linux/](https://www.tecmint.com/xz-command-examples-in-linux/)

[https://www.debian.org/releases/wheezy/amd64/apds01.html.en](https://www.debian.org/releases/wheezy/amd64/apds01.html.en)

[https://linuxjourney.com/lesson/device-types](https://linuxjourney.com/lesson/device-types)

[https://www.geeksforgeeks.org/dd-command-linux/](https://www.geeksforgeeks.org/dd-command-linux/)

[https://linoxide.com/linux-command/linux-dd-command-create-1gb-file/](https://linoxide.com/linux-command/linux-dd-command-create-1gb-file/)

``[`https://www.geeksforgeeks.org/cpio-command-in-linux-with-examples/`](https://www.geeksforgeeks.org/cpio-command-in-linux-with-examples/)``

and whith the special thanks from shawn powers.
