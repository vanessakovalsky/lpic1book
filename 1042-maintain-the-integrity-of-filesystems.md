# 104.2. Maintenance de l'intégrité des systèmes de fichiers

**Poids:** 2

**Description:** Les candidats doivent être en mesure de maintenir l'intégrité d'un système de fichiers standard, ainsi que les informations supplémentaires associées à la journalisation.

**Connaissances clés:**

* Vérification de l'intégrité des systèmes de fichiers.
* Contrôle de l'espace et des inodes libres.
* Réparation de problèmes élémentaires sur les système de fichiers. 

**Concepts et Utilitaires:**

* du
* df
* fsck
* e2fsck
* mke2fs
* debugfs
* tune2fs
* XFS tools (such as xfs_metadump and xfs_info)



Dans le cas où le système crash ou n'a plus de courant, votre système de ficheir peut être laissé dans un état inconsistant, avec certains changements complets et d'autre non. 

Le système d'exploitation avec un système de fichier endommagé n'est pas une bonne idér car vous pourriez générer des erreurs ou rater certaines erreurs existantes. Nous allons voir quelques outils pour gérer de tels problèmes.

{% hint style="danger" %}
Vous devriez toujours sauvegarder votre système de fichier avant d'essayer de le réparer!
{% endhint %}

### fsck

 L'outil principal pour vérifier et réparer des système de fichiers est `fsck`, qui, comme `mkfs`, est un frontal pour les routines de vérification de systèmes de fichiers pour de nombreux types de systèmes de fichiers.

```
root@ubuntu16-1:~# fsck
fsck          fsck.ext3     fsck.fat      fsck.nfs      
fsck.cramfs   fsck.ext4     fsck.minix    fsck.vfat     
fsck.ext2     fsck.ext4dev  fsck.msdos 

root@ubuntu16-1:~# ls /sbin/fsck*
/sbin/fsck         /sbin/fsck.ext3     /sbin/fsck.fat    /sbin/fsck.nfs
/sbin/fsck.cramfs  /sbin/fsck.ext4     /sbin/fsck.minix  /sbin/fsck.vfat
/sbin/fsck.ext2    /sbin/fsck.ext4dev  /sbin/fsck.msdos
```

> Certains sont juste des liens vers la commande e2fsck et ils sont les mêmes.

La commande fsck dans Linux nous permet de manuellement vérifier pour les inconsistences d'un système de fichiers. La commande fsck a besoint d'être lancer avec les droits d'administrateur ou en tant que **root** :

```
root@ubuntu16-1:~# fsck /dev/sda1
fsck from util-linux 2.27.1
e2fsck 1.42.13 (17-May-2015)
/dev/sda1 is mounted.
e2fsck: Cannot continue, aborting.
```

{% hint style="danger" %}
Afin d'utiliser fsck la partition ne doit pas être monté, autrement elle peut faire des dommages!
{% endhint %}

Vérifions simplement un système de fichier une partition non monté ext3 (sdb1) et essayons de réparer les erreurs :

```
root@ubuntu16-1:~# fsck /dev/sdb1
fsck from util-linux 2.27.1
e2fsck 1.42.13 (17-May-2015)
/dev/sdb1: clean, 11/1310720 files, 126322/5242624 blocks
```

 Cette commande essaiera de vérifeir **/dev/sdb1**, et rapportera toute erreur qu'elle trouve. Le code de sortie retourné par fsck est un des suivants :

* 0 Pas d'erreurs
* 1 Erreurs du système de fichier corrigées 
* 2 Le système doit être redémarré 
* 4 Erreurs sur le système de fichier qui reste non corrigées 
* 8 Erreur opérationel 
* 16 Erreur d'utilisation ou de syntaxe 
* 32 Vérification annulé par demande de l'utilisateur 
* 128 Erreur sur les bibliothèques partagées

 L'option `-N` nous montre ce qui aurait dû être exécuter mais n'essaie pas de les réparer:

```
root@ubuntu16-1:~# fsck -N /dev/sdb1
fsck from util-linux 2.27.1
[/sbin/fsck.ext3 (1) -- /dev/sdb1] fsck.ext3 /dev/sdb1
```

 `-n ` fait que la commande ne fixe rien et montre seulement ce qu'elle va faire:

```
root@ubuntu16-1:~# fsck -n /dev/sdb1
fsck from util-linux 2.27.1
e2fsck 1.42.13 (17-May-2015)
/dev/sdb1: clean, 11/1310720 files, 126322/5242624 blocks
```

Normalement, **fsck** passe les parties du système de fichier marquée comme "clean" — ce qui signifie toutes les écritures en cours qui ont été faite avec succès. L'option **-f** ("force") spécifie que **fsck** devrait vérifier toutes les parties du système de fichier. Le resultat est moins efficace mais vérifie plus en profondeur.

```
root@ubuntu16-1:~# fsck -f /dev/sdb1
fsck from util-linux 2.27.1
e2fsck 1.42.13 (17-May-2015)
Pass 1: Checking inodes, blocks, and sizes
Pass 2: Checking directory structure
Pass 3: Checking directory connectivity
Pass 4: Checking reference counts
Pass 5: Checking group summary information
/dev/sdb1: 11/1310720 files (0.0% non-contiguous), 126322/5242624 blocks

```

{% hint style="success" %}
**Qu'est ce que les inodes?**

Comme nous l'avons vu Linux traite tout comme un fichier (même les périphériques matériels). Le clavier, la souris, les imprimantes, les écrans, les disques du, les processus, et même les dossiers sont traités comme des fichiers ans Linux. Les fichiers réguliers contiennent des données comme du texte, de la musique, des vidéos, etc. En plus des données régulières, il y a d'autre données à propos de ces ficheirs, comme leur taille, leur propriétaire, leurs permissions, le timestamps, etc. Ces métadonnés à propos d'un fichier sont gérée comme structure de données connu comme un inode (index node).
{% endhint %}

> Nous pouvons suassi vérifier les systèmes de fichiers fichiers en utilisant leur UUID.(Utiliser la commande blkid ):
>
> ```
> root@ubuntu16-1:~# blkid /dev/sdb1
> /dev/sdb1: UUID="b9ca56aa-4ea3-4f61-92c8-18c867fd991f" SEC_TYPE="ext2" TYPE="ext3" PARTUUID="4d71bc84-01"
> root@ubuntu16-1:~# fsck -N UUID="b9ca56aa-4ea3-4f61-92c8-18c867fd991f"
> fsck from util-linux 2.27.1
> e2fsck 1.42.13 (17-May-2015)
> /dev/sdb1: clean, 11/1310720 files, 126322/5242624 blocks
> ```

| fsck command example   | description                                  |
| ---------------------- | -------------------------------------------- |
| fsck -M /dev/sda1      | Empêche de lancer fsck sur un système de fichier monté |
| fsck -t ext3 /dev/sdb1 | Vérifie seulement un type spécifique de système de fichier        |
| fsck -y -f /dev/sdb1   | Réponds “yes” à toutes les questions pour réparer       |

> Pour vérifier un système de fichier XFS, nous devons utiliser la commande xfs_check

## Outils avancé

Il y a plusieurs outils avancé que nous pouvons utiliser pour examiner ou réparer un système de fichier.

#### Outils pour les système de ficheir ext2 et ext3 <a href="tools-for-ext2-and-ext3-filesystems" id="tools-for-ext2-and-ext3-filesystems"></a>

* **tune2fs:** Ajuste les paramètre sur les systèmes de fichier ext2 et ext3. Utiliser pour ajouter un journal
* **debugfs:** Editeur de système de fichier interactif

{% hint style="info" %}
**Super Blocs**

 Vous pouvez vous demander comme tous ces outils de vérification et de réparation savent par où commencer. Les systèmes de fichiers de Linux et UNIX ont un _superblock_, qui décrit les _metadonnées_ du système de fichier, ou des données qui décrivent le système de fichier lui-même. C'est généralement stocké dans un endroit connu, souvent au début ou proche du début du système de fichier, et répliqué dans d'autres endroits bien connus. Vous pouvez utiliser l'option `-n` de  `mke2fs` pour afficher l'endroit où sont les superblocs pour un système de fichier existant.

```
root@ubuntu16-1:~# mke2fs -n /dev/sdb1
mke2fs 1.42.13 (17-May-2015)
/dev/sdb1 contains a ext3 file system
	created on Sat Jan 25 22:17:24 2020
Proceed anyway? (y,n) y
Creating filesystem with 5242624 4k blocks and 1310720 inodes
Filesystem UUID: 28ce54ac-abbd-4788-bab8-b71eae9a53bc
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
	4096000
```
{% endhint %}

### tune2fs

 La famille ext de système de fichier a aussi un utilitaire appelé `tune2fs`, qui peut être utilisé pour inspecter les informations sur le nombre de blocs ainsi que les information sur le fait que le système de fichiers soit journalisé (ext3 or ext4) ou non  (ext2). 

{% hint style="success" %}
La commande tune2fs vous permet de définir des paramètres pour un système de fichier sur un système de fichiers ext2 ou ext3 monté.
{% endhint %}

`-l` montre le contenu du superbloc du système de fichier, incluant les valeurs courantes de ses paramètres :

```
root@ubuntu16-1:~# tune2fs -l /dev/sdb1
tune2fs 1.42.13 (17-May-2015)
Filesystem volume name:   <none>
Last mounted on:          <not available>
Filesystem UUID:          b9ca56aa-4ea3-4f61-92c8-18c867fd991f
Filesystem magic number:  0xEF53
Filesystem revision #:    1 (dynamic)
Filesystem features:      has_journal ext_attr resize_inode dir_index filetype sparse_super large_file
Filesystem flags:         signed_directory_hash 
Default mount options:    user_xattr acl
Filesystem state:         clean
Errors behavior:          Continue
Filesystem OS type:       Linux
Inode count:              1310720
Block count:              5242624
Reserved block count:     262131
Free blocks:              5116302
Free inodes:              1310709
First block:              0
Block size:               4096
Fragment size:            4096
Reserved GDT blocks:      1022
Blocks per group:         32768
Fragments per group:      32768
Inodes per group:         8192
Inode blocks per group:   512
Filesystem created:       Wed Jan 22 23:42:22 2020
Last mount time:          n/a
Last write time:          Thu Jan 23 02:21:07 2020
Mount count:              0
Maximum mount count:      -1
Last checked:             Thu Jan 23 02:21:07 2020
Check interval:           0 (<none>)
Reserved blocks uid:      0 (user root)
Reserved blocks gid:      0 (group root)
First inode:              11
Inode size:	          256
Required extra isize:     28
Desired extra isize:      28
Journal inode:            8
Default directory hash:   half_md4
Directory Hash Seed:      73007ad4-213f-44cd-b8f0-4fbd0c1e90b2
Journal backup:           inode blocks

```

> La commande peut aussi être utilisé pour définir de nombreux paramètres ou convertir un système de fichier ext2 en ext3 en ajoutant un journal en utilisant l'option `-j`:` tune2fs -j /dev/sdd1`

Nous pouvons aussi utiliser tune2fs pour modifier le label de la partition :

```
root@ubuntu16-1:~# tune2fs -L myroot /dev/sda1
tune2fs 1.42.13 (17-May-2015)
```

### debugfs

Est un débugueur interactif de système de fichier. Utiliser le pour examiner ou changer l'état d'un système de fichier ext2 ou ext3. Il ouvre le système de fichier en mode lecture seule sauf si vous lui dites de ne pas le faire (avec l'option `-w`).

{% hint style="danger" %}
 Si le système de fichiers est monté, c'est ok de l'inspecter, mais **n'essayez pas** de le réparer.
{% endhint %}

```
root@ubuntu16-1:~# debugfs /dev/sda1
debugfs 1.42.13 (17-May-2015)
debugfs:  cd /etc/
debugfs:  pwd
[pwd]   INODE: 1179649  PATH: /etc
[root]  INODE:      2  PATH: /
debugfs:  stat passwd

Inode: 1187630   Type: regular    Mode:  0644   Flags: 0x80000
Generation: 2904697195    Version: 0x00000000:00000001
User:     0   Group:     0   Size: 2388
File ACL: 0    Directory ACL: 0
Links: 1   Blockcount: 8
Fragment:  Address: 0    Number: 0    Size: 0
 ctime: 0x5e2064c5:64a1019c -- Thu Jan 16 16:57:33 2020
 atime: 0x5e2cb8a0:4aa345f8 -- Sun Jan 26 01:22:32 2020
 mtime: 0x5e2064c5:64a1019c -- Thu Jan 16 16:57:33 2020
crtime: 0x5e2064c5:64a1019c -- Thu Jan 16 16:57:33 2020
Size of extra inode fields: 32
EXTENTS:
(0):4265700

debugfs:  ncheck 1187630
Inode	Pathname
1187630	/etc/passwd
debugfs:  q
```

### xfs_info

Le système de fichier xfs a sa propre famille de commande. xfs_info est le même que tune2fs mais pour xfs.

{% hint style="success" %}
xfs_info devrait être utilisé sur un système de fichiers monté.
{% endhint %}

> Le paquet xfsprogs doit être installé 

```
root@ubuntu16-1:~# xfs_info /dev/sdb1
meta-data=/dev/sdb1              isize=512    agcount=4, agsize=1310656 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1 spinodes=0
data     =                       bsize=4096   blocks=5242624, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal               bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```

| xfs command  | description                   |
| ------------ | ----------------------------- |
| xfs_info     | Montre les informations             |
| xfs_check    | Vérification complète du système de fichier |
| xfs_repair   | Vérifie et fixe les problèmes      |
| xfs_check -n | Comme xfs_check             |

```
root@ubuntu16-1:~# xfs_repair -n /dev/sdb1
xfs_repair: /dev/sdb1 contains a mounted and writable filesystem

fatal error -- couldn't initialize XFS library
```

{% hint style="danger" %}
xfs_repair ne fonctionne pas sur les systèmes de fichiers monté.
{% endhint %}

```
root@ubuntu16-1:~# xfs_repair  /dev/sdb1
Phase 1 - find and verify superblock...
Phase 2 - using internal log
        - zero log...
        - scan filesystem freespace and inode maps...
        - found root inode chunk
Phase 3 - for each AG...
        - scan and clear agi unlinked lists...
        - process known inodes and perform inode discovery...
        - agno = 0
        - agno = 1
        - agno = 2
        - agno = 3
        - process newly discovered inodes...
Phase 4 - check for duplicate blocks...
        - setting up duplicate extent list...
        - check for inodes claiming duplicate blocks...
        - agno = 0
        - agno = 1
        - agno = 2
        - agno = 3
Phase 5 - rebuild AG headers and trees...
        - reset superblock...
Phase 6 - check inode connectivity...
        - resetting contents of realtime bitmap and summary inodes
        - traversing filesystem ...
        - traversal finished ...
        - moving disconnected inodes to lost+found ...
Phase 7 - verify and correct link counts...
done
```

> Dans XFS, vous pouvez aussi étendre le système de fichier et ne pouvez pas le réduire.

### Surveiller l'espace libre <a href="monitoring-free-space" id="monitoring-free-space"></a>

 Sur un périphérique de stockage, un fichier ou un dossier est contenu dans une collection de _blocs_. Les informations du fichier sont contenu dans un _inode._

> **Rappel :** les inodes garde les information comme qui est le propriétaire, quand le fichier a été accèdé la dernière fois, quelle taille fait t'il, est ce que c'est un dosier et qui peut le lire ou l'écrire. Le numéro inode est aussi connu comme le numéro de série du fichier et est unique sur un système de fichier particulier.

Les blocs de données et les inodes prennent chacun de l'espace sur un système de fichier, donc nous devons surveiller l'utilisation de l'espace pour s'assurer que notre système de fichier a de l'espace pour grossir.

### df

La commande `df  `(DiskFree) est utilisé pour trouvé l'espace libre et utilisé d'un système de fichier.

```
df [OPTION]...[FILE]...
```

 Si aucun nom de fichier n'est passé comme argument avec la commande df alors il montre l'espace disponible sur les systèmes de fichiers **montés** actuellement

```
root@ubuntu16-1:~# df
Filesystem     1K-blocks    Used Available Use% Mounted on
udev              462796       0    462796   0% /dev
tmpfs              98508    6544     91964   7% /run
/dev/sda1       50442940 5613840  42243708  12% /
tmpfs             492540     284    492256   1% /dev/shm
tmpfs               5120       4      5116   1% /run/lock
tmpfs             492540       0    492540   0% /sys/fs/cgroup
tmpfs              98508      48     98460   1% /run/user/1001
```

\-T  affiche le type de système de fichier,  -h, –human-readable affiche la taille (en puissance de 1024):

```
root@ubuntu16-1:~# df -Th
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  452M     0  452M   0% /dev
tmpfs          tmpfs      97M  6.4M   90M   7% /run
/dev/sda1      ext4       49G  5.4G   41G  12% /
tmpfs          tmpfs     481M  284K  481M   1% /dev/shm
tmpfs          tmpfs     5.0M  4.0K  5.0M   1% /run/lock
tmpfs          tmpfs     481M     0  481M   0% /sys/fs/cgroup
tmpfs          tmpfs      97M   48K   97M   1% /run/user/1001
```

> `-H` rend les nombres lisible par un humain aussi  (en puissance de 1000).

\-i liste l'information sur l'inode au lieu de l'utilisation des blocs :

```
root@ubuntu16-1:~# df -i
Filesystem      Inodes  IUsed   IFree IUse% Mounted on
udev            115699    456  115243    1% /dev
tmpfs           123135    750  122385    1% /run
/dev/sda1      3211264 256212 2955052    8% /
tmpfs           123135      9  123126    1% /dev/shm
tmpfs           123135      6  123129    1% /run/lock
tmpfs           123135     17  123118    1% /sys/fs/cgroup
tmpfs           123135     27  123108    1% /run/user/1001
```

> Vous vous rappelez ?  Il n'y a pas de propriétaire ou de permissions sur les systèmes de fichiers vfat. Le format de fichier vfat n'a pas d'inodes!

| df command example | description                                                                        |
| ------------------ | ---------------------------------------------------------------------------------- |
| df -a              | Affiche toutes les informations incluant les pseudo, les duplications et les systèmes de fichiers inacessible. |
| df -Th /home       | Afiche les information du système de fichier /home                                           |
| df -k or -m or -h  | Affiche les information en Bytes, MB , GB                                             |
| df -t ext3         | Inclure un type de système de fichier spécifique                                                  |
| df -x xfs          | Exclure un type de système de fichier spécifique                                                  |

 La commande `df` donne des information sur un système de fichier entier. Parfois vous voudrez savoir combien d'espace est utilisé par un fichier ou un dossier spécifique. Pour répondre à cette question, nous utilisons la commande `du`.

### du

 La commande Linux `du `(Disk Usage), est utiliser pour vérifier l'information d'**utilisation du disque par les fichiers et dossiers** sur une machine.

```
du [OPTION]... [FILE]...
```

| useful switch | description                                                     |
| ------------- | --------------------------------------------------------------- |
| -a            | Ecrit le nombre de tous les fichiers, pas seulement les dossiers                  |
| -h            | Lisible par un humain. Signifie que nous voyons les tailles en Bytes, KB, MB, GB,... |
| -c            | Total complet de l'utilisation de l'espace disque sur la dernière ligne                   |
|  –max-depth=N | Allez à N ou moins de sous-dossier plus bas                           |
| -s            | Affiche seulement le total pour chaque dossier                           |

```
root@ubuntu16-1:~# du
16	./.aptitude
8	./.cache/dconf
12	./.cache
8	./.config/htop
8	./.config/gedit
8	./.config/dconf
4	./.config/ibus/bus
8	./.config/ibus
36	./.config
4	./.gnupg/private-keys-v1.d
48	./.gnupg
4	./.gconf
12	./.elinks
16	./.ssh
4	./.nano
8	./.local/share
12	./.local
336	./backup
8	./.dbus/session-bus
12	./.dbus
4	./test-space
932	.

root@ubuntu16-1:~# du -sh
932K	.
```

L'option **--time** est utilisé pour afficher l'heure de dernière modification sur la sortie de  du.

**--exclude=PATTERN** exclut les fichiers qui correspondent à PATTERN. Exemple : `du -ah --exclude="*.txt" /home/payam`


.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-104-2/](https://developer.ibm.com/tutorials/l-lpic1-104-2/)

[https://www.thegeekstuff.com/2012/08/fsck-command-examples/](https://www.thegeekstuff.com/2012/08/fsck-command-examples/)

[https://www.computerhope.com/unix/fsck.htm](https://www.computerhope.com/unix/fsck.htm)

[https://www.geeksforgeeks.org/file-system-consistency-checker-fsck/](https://www.geeksforgeeks.org/file-system-consistency-checker-fsck/)[https://www.serverwatch.com/tutorials/article.php/3797306/tune2fs-Makes-It-Easy-to-Play-With-Filesystems.htm](https://www.serverwatch.com/tutorials/article.php/3797306/tune2fs-Makes-It-Easy-to-Play-With-Filesystems.htm)[https://jadi.gitbooks.io/lpic1/content/1042\_maintain_the_integrity_of_filesystems.html](https://jadi.gitbooks.io/lpic1/content/1042\_maintain_the_integrity_of_filesystems.html)

[https://linoxide.com/linux-command/linux-inode/](https://linoxide.com/linux-command/linux-inode/)

[https://www.geeksforgeeks.org/dumpe2fs-command-in-linux-with-examples/](https://www.geeksforgeeks.org/dumpe2fs-command-in-linux-with-examples/)

[https://www.geeksforgeeks.org/dumpe2fs-command-in-linux-with-examples/](https://www.geeksforgeeks.org/dumpe2fs-command-in-linux-with-examples/)

[https://www.geeksforgeeks.org/df-command-in-linux-with-examples/](https://www.geeksforgeeks.org/df-command-in-linux-with-examples/)

[https://www.geeksforgeeks.org/df-command-linux-examples/](https://www.geeksforgeeks.org/df-command-linux-examples/)

[https://www.tecmint.com/how-to-check-disk-space-in-linux/](https://www.tecmint.com/how-to-check-disk-space-in-linux/)

[https://www.geeksforgeeks.org/du-command-linux/](https://www.geeksforgeeks.org/du-command-linux/)

[https://www.geeksforgeeks.org/du-command-linux-examples/](https://www.geeksforgeeks.org/du-command-linux-examples/)

[https://www.tecmint.com/check-linux-disk-usage-of-files-and-directories/](https://www.tecmint.com/check-linux-disk-usage-of-files-and-directories/)

With the special thanks of shawn powers.

.

.

