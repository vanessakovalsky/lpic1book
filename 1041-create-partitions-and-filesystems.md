# 104.1. Création des partitions et des systèmes de fichiers

## **104.1 Création des partitions et des systèmes de fichiers**

**Poids:** 2

**Description:** Les candidats doivent être en mesure de configurer le partitionnement des disques puis de créer des systèmes de fichiers sur des supports comme les disques durs. Ceci inclut la prise en charge des partitions d'échange (swap). 

**Connaissances clés:**

* Gestion des tables de partition MBR et GPT
* Utilisation des différentes commandes mkfs pour le paramétrage des partitions et la création des différents systèmes de fichiers comme :
  * ext2/ext3/ext4
  * XFS
  * VFAT
  * exFAT
* Connaissance de base de Btrfs, y compris les systèmes de fichiers sur plusieurs périphériques, la compression et les sous-volumes.

**Concepts et Utilitaires:**

* fdisk
* gdisk
* parted
* mkfs
* mkswap



### BIOS

 Le Basic Input/Output System (BIOS), (aussi connu comme System BIOS, ROM BIOS ) est un standrd pour définir l'interface du matériel. Le logiciel VIOS est construit sur le PC, et est le premier logiciel lancé lorsqu'un PC est allumé.

L'objectif fondamental du BIOS est d'initiliser et de tester les composants matériel du système, et de charger un chargeur de démarage ou un système d'exploitation depuis un périphérique de mémoire de masse. 

### UEFI

Le Unified Extensible Firmware Interface (UEFI) est une specification qui définit une interface logiciel entre un système d'exploitatio et la plateforme matériel.  UEFI est fait pour remplacer l'interface matérielle Basic Input/Output System (BIOS). En pratique, la plupart des images UEFI fournit un support pour les services hérités du BIOS. UEFI peut supporté le diagnostique à distance et la réparation des ordinateurs, même sans autre système d'exploitation!

{% hint style="info" %}
> La spécification originale EFI (Extensible Firmware Interface) a été developpé par Intel. UEFI n'est pas encore largement diffusé et la plupart des fabricant matériel ont migré et utilise presque exclusivement UEFI. Les carte mère plus vieilles et les moins chères utilise encore le système BIOSM.
{% endhint %}

### MBR

Un master boot record (MBR) est un type spécial de secteur de démarrage au tout début des périphériques de stockage de masse comme les disques dur ou les disques externes. 

Le MBR contient des information sur comment les partitions logiques, contenant les fichiers du système, sont organisé sur ce média. De plus le MBR contient également du code exécutable comme un chargeur pour le système d'exploitation installé qui permet au système de passé à sa seconde étape de chargement. Ce code MBR est souvent appelé chargeur de démarrage.

![](.gitbook/assets/bootloader-mbr.jpg)

**Master Boot records a de nombreuses limitation:**

* MBR met toutes les informations dans le premier secteur du disque dur, de sorte à ce qu'un problème apparaiise sur ce système, le système ne pourra pas démarré. 
* MBR contient seulement quatre entrée (slots) pour quatre partitions Primaires, l'une d'elle peut être une partition étendue. Cette partition contien de l'espace non alloué dans lequel nous pouvons créé un nombre illimité de partition Logique.

![](.gitbook/assets/createpartition-mbrpart.jpg)

* L'organisation de la table de partition sur le MBR limit l'espace de stockage maximum adressable à un disque de 2 TB. 

De fait, le schema de partitionnement basé sur MBR est un processus qui est surcharché par le schéma GUID Partition Table (GPT) sur les nouveaux ordinateurs. Un GPT  peut coexister avec un MBR de sorte à fournir une forme de compatibilité ascendante pour les plus vieux systèmes. 

### GPT

GUID Partition Table (GPT) est un standard pour la conception des tables de partitions sur un disque dur physique, utilisant des identificants globals unique (GUID).

 Bien qu'il fasse partie du standard de l'interface matérielle de Unified Extensible Firmware Interface (UEFI), il est aussi utilisé sur certains système BIOS à cause des limitations de la table de partition de MBR.

### MBR vs GBT

| MBR(Master Boot Record)                                                   | GPT(GUID Partition Table)                                             |
| ------------------------------------------------------------------------- | --------------------------------------------------------------------- |
| Depuis 1983                                                                | New 2005-...                                                          |
| Vit dans le premier secteur                                               | Stocké à plusieurs endroits du périphérique                           |
| <p>Limité à 4 partitions par disque</p><p>Limité à des partitions de 2 TB </p> | <p>Supporte 128 partitions par disque</p><p>Supporte des partitions de 18EX </p> |

> A la fois les disques GPT et les disques MBR peuvent être basique ou dynamique.

### BIOS vs UEFI

| BIOS(Basic Input/Output System)                  | UEFI(Unified Extensible Frimware Interface)       |
| ------------------------------------------------ | ------------------------------------------------- |
| Supporte seulement MBR( Donc GPT agit comme s'il était MBR!) | Support natif de GPT                  |
| Voie GPT comme un périphérique avec une partition unique MBR  | A besoin d'être lancé en mode legacy pour supporter MBR |

{% hint style="success" %}
Généralement, MBR et BIOS **(MBR + BIOS)**, et GPT et UEFI **(GPT + UEFI)** vont de pair. C'est requis pour certains système (Windows), alors que c'est optionnel pour d'autres (Linux).
{% endhint %}

### Bloc de périphérique

Un bloc de périphérique est une couche d'abstration pour n'importe quel périphérique de stockage qui peut être formaté en blocs de taille fixe et les blocs doivent pouvoir être accéder aléatoirement.

 Exemple de bloc de périphérique inclue le premier disque dur IDE ou SATA de notre système (/dev/sda ou /dev/hda) ou le second SCSI, IDE, ou périphérique USB (/dev/sdb). Utiliser la commande `ls -l` pour afficher les entrées de /dev.

```
root@ubuntu16-1:~# ls -l  /dev/null /dev/sd[a-z] /dev/sr0 /dev/tty0
crw-rw-rw-  1 root root   1, 3 Dec  2  2018 /dev/null
brw-rw----  1 root disk   8, 0 Dec  2  2018 /dev/sda
brw-rw----+ 1 root cdrom 11, 0 Dec  2  2018 /dev/sr0
crw--w----  1 root tty    4, 0 Dec  2  2018 /dev/tty0
```

 Le premier caracère pour chaque ligne de la sortie est  **b** pour un périphérique de **block**, comme une disquette, un lecteur CD, un disque dur IDE ou  un disque dur SCSI; et **c** pour un périphérique **character**, comme un terminal (tty) ou le périphérique null.

#### Partitionnemen de disque

Maintenant que nous avons présenté les modèles de disques durs (MBR & GPT) ,apprenons comment crée des partitions MBR en utilisant fdisk et des partitions GPT en utilisant gdisk.

### fdisk

**fdisk** aussi connu comme format disk est une commande interractive dans Linux utilisé pour créer et manipuler une table de partition. Il est utilisé pour voir, créé, supprimer, modifier, redimensionner, copier et déplacer les partitions sur un disque dur en utilisant l'interface interractive.\
 fdisk nous permet de créer un maximum de quatre partitions primaires et le nombre de partitions logiques dépends de la taille du disque dur que vous utiliser. Il permet à l'utilisateur :

* De créer de l'espace pour de nouvelles partitions.
* Organiser l'espace pour les nouveaux périphériques.
* Ré-organiser les anciens périphériques.
* Copier ou déplacer les données vers un nouveau disque(partitions)

```
fdsik [options] device
or
fdisk -l [device...]
```

La première chose à faire avant autre chose avec les disque et partition est de voir les déails basiques à propos de toutes les partitions disponibles sur le système en utilisation l'option ` -l`:

```
root@ubuntu16-1:~# fdisk -l
Disk /dev/sda: 50 GiB, 53687091200 bytes, 104857600 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x101c66bb

Device     Boot     Start       End   Sectors  Size Id Type
/dev/sda1  *         2048 102762495 102760448   49G 83 Linux
/dev/sda2       102764542 104855551   2091010 1021M  5 Extended
/dev/sda5       102764544 104855551   2091008 1021M 82 Linux swap / Solaris


Disk /dev/sdb: 20 GiB, 21474836480 bytes, 41943040 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

Comme vous pouvez le voir, nous avons deux périphériques de disques (sda,sdb). Sda a quelque parititon mais sdb est vide.

*  **Boot** : La colonne Boot est la première partition, /dev/sda1, l'asterisque **(\*)** indique que cette partition contient les fichiers requis par le chargeur de démarrage pour démarrer le système.
*  **Start and End** : Les colonnes start et end liste les secteur de démarrage et de fin de chaque partition.
*  **Blocks** : La colonne blocks liste le nombre de blocs alloués à la partition.
*  **Id and System** : Ces colonnes identifie le type de partition.

Voir les partitions sur un disque spécifique (sda) :

```
root@ubuntu16-1:~# fdisk -l /dev/sda
Disk /dev/sda: 50 GiB, 53687091200 bytes, 104857600 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x101c66bb

Device     Boot     Start       End   Sectors  Size Id Type
/dev/sda1  *         2048 102762495 102760448   49G 83 Linux
/dev/sda2       102764542 104855551   2091010 1021M  5 Extended
/dev/sda5       102764544 104855551   2091008 1021M 82 Linux swap / Solaris
```

Démarraons le mode interractif et voyons les commandes disponible (sdb):

```
root@ubuntu16-1:~# fdisk /dev/sdb

Welcome to fdisk (util-linux 2.27.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0xbd67d3c2.

Command (m for help): m

Help:

  DOS (MBR)
   a   toggle a bootable flag
   b   edit nested BSD disklabel
   c   toggle the dos compatibility flag

  Generic
   d   delete a partition
   F   list free unpartitioned space
   l   list known partition types
   n   add a new partition
   p   print the partition table
   t   change a partition type
   v   verify the partition table
   i   print information about a partition

  Misc
   m   print this menu
   u   change display/entry units
   x   extra functionality (experts only)

  Script
   I   load disk layout from sfdisk script file
   O   dump disk layout to sfdisk script file

  Save & Exit
   w   write table to disk and exit
   q   quit without saving changes

  Create a new label
   g   create a new empty GPT partition table
   G   create a new empty SGI (IRIX) partition table
   o   create a new empty DOS partition table
   s   create a new empty Sun partition table


Command (m for help): 
```

 Création une partition:

```
Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-41943039, default 2048): 
Last sector, +sectors or +size{K,M,G,T,P} (2048-41943039, default 41943039): 

Created a new partition 1 of type 'Linux' and of size 20 GiB.

Command (m for help): 
```

Puis nous allons spécifié le type de partition en se basant sur l'utilisation future que nous avons prévu :

```
Command (m for help): l

 0  Empty           24  NEC DOS         81  Minix / old Lin bf  Solaris        
 1  FAT12           27  Hidden NTFS Win 82  Linux swap / So c1  DRDOS/sec (FAT-
 2  XENIX root      39  Plan 9          83  Linux           c4  DRDOS/sec (FAT-
 3  XENIX usr       3c  PartitionMagic  84  OS/2 hidden or  c6  DRDOS/sec (FAT-
 4  FAT16 <32M      40  Venix 80286     85  Linux extended  c7  Syrinx         
 5  Extended        41  PPC PReP Boot   86  NTFS volume set da  Non-FS data    
 6  FAT16           42  SFS             87  NTFS volume set db  CP/M / CTOS / .
 7  HPFS/NTFS/exFAT 4d  QNX4.x          88  Linux plaintext de  Dell Utility   
 8  AIX             4e  QNX4.x 2nd part 8e  Linux LVM       df  BootIt         
 9  AIX bootable    4f  QNX4.x 3rd part 93  Amoeba          e1  DOS access     
 a  OS/2 Boot Manag 50  OnTrack DM      94  Amoeba BBT      e3  DOS R/O        
 b  W95 FAT32       51  OnTrack DM6 Aux 9f  BSD/OS          e4  SpeedStor      
 c  W95 FAT32 (LBA) 52  CP/M            a0  IBM Thinkpad hi ea  Rufus alignment
 e  W95 FAT16 (LBA) 53  OnTrack DM6 Aux a5  FreeBSD         eb  BeOS fs        
 f  W95 Ext'd (LBA) 54  OnTrackDM6      a6  OpenBSD         ee  GPT            
10  OPUS            55  EZ-Drive        a7  NeXTSTEP        ef  EFI (FAT-12/16/
11  Hidden FAT12    56  Golden Bow      a8  Darwin UFS      f0  Linux/PA-RISC b
12  Compaq diagnost 5c  Priam Edisk     a9  NetBSD          f1  SpeedStor      
14  Hidden FAT16 <3 61  SpeedStor       ab  Darwin boot     f4  SpeedStor      
16  Hidden FAT16    63  GNU HURD or Sys af  HFS / HFS+      f2  DOS secondary  
17  Hidden HPFS/NTF 64  Novell Netware  b7  BSDI fs         fb  VMware VMFS    
18  AST SmartSleep  65  Novell Netware  b8  BSDI swap       fc  VMware VMKCORE 
1b  Hidden W95 FAT3 70  DiskSecure Mult bb  Boot Wizard hid fd  Linux raid auto
1c  Hidden W95 FAT3 75  PC/IX           bc  Acronis FAT32 L fe  LANstep        
1e  Hidden W95 FAT1 80  Old Minix       be  Solaris boot    ff  BBT   
```

**Types de Partition**

Les types de partition peuvent être affichés et modifiés en utilisant l'utilitaire fdisk. Une liste partielle (les plus utilisés) des types de partitions est:\
* **83: Linux**
* **82: Linux swap**
* **5: Extended**
* **8e: Linux LVM**

```
Command (m for help): t

Selected partition 1
Partition type (type L to list all types): 83
Changed type of partition 'Linux' to 'Linux'.
```

Et nous utilisons l'option `-p` afin d'afficher la table de partition :

```
Command (m for help): p
Disk /dev/sdb: 20 GiB, 21474836480 bytes, 41943040 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xbd67d3c2

Device     Boot Start      End  Sectors Size Id Type
/dev/sdb1        2048 41943039 41940992  20G 83 Linux

Command (m for help): 
```

fdisk n'écrit pas les modifications sur le disque dur tant que vous ne lui demander par en utilisant l'option `w`, si vous n'êtes pas sur utiliser `q` pour quitter et le disque dur ne sera pas modifié! 

```
Command (m for help): v

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```

Utiliser `-d` pour supprimer une partition, soyez prudent!

> Pour voir le message d'aide et lister toutes les options, utiliser la commande `fdisk -h`.

### gdisk

Nous pouvon gérer les partitions GPT avec gdisk. Comme fdisk, gdisk est un programme en mode texte avec des menus pour créer et manipuler la table de partition. Il convertira automatique une table de partition ancienne de type Master Boot Record (MBR) cer le format plus récent de table de partition Globally Unique Identifier (GUID) Partition Table (GPT), ou il chargera une table de partition GPT.

```
root@ubuntu16-1:~# gdisk /dev/sdb
GPT fdisk (gdisk) version 1.0.1

Partition table scan:
  MBR: MBR only
  BSD: not present
  APM: not present
  GPT: not present


***************************************************************
Found invalid GPT and valid MBR; converting MBR to GPT format
in memory. THIS OPERATION IS POTENTIALLY DESTRUCTIVE! Exit by
typing 'q' if you don't want to convert your MBR partitions
to GPT format!
***************************************************************


Warning! Secondary partition table overlaps the last partition by
33 blocks!
You will need to delete this partition or resize it in another utility.

Command (? for help): ?
b	back up GPT data to a file
c	change a partition's name
d	delete a partition
i	show detailed information on a partition
l	list known partition types
n	add a new partition
o	create a new empty GUID partition table (GPT)
p	print the partition table
q	quit without saving changes
r	recovery and transformation options (experts only)
s	sort partitions
t	change a partition's type code
v	verify disk
w	write table to disk and exit
x	extra functionality (experts only)
?	print this menu

Command (? for help): q
root@ubuntu16-1:~# 
```

### parted

 La commande `parted` est un éditeur de partition qui fonctionnera pour formatter les disques MBR et GPT..

### Système de fichier

Le système de fichier Linux ou tout système de fichier est une couche qui est sous le système d'exploitation et qui gère le positionnement de vos données sur le stockage, sans lui, le système ne peut pas savoir où les fichiers commence et se termine.

### **Type de système de fichiers**

 Linux supporte différent système de fichier. Chacun a des forces et des fabiblesse et son propre ensemble de caractèristiques de performance. 

**Ext, Ext2, Ext3, Ext4, JFS, XFS, btrfs et swap**

Un des attribut important du système de fichier est la journalisation

**Qu'est ce que la journalisation ?**

La journalisation désigne la manière d'empêcher la corruption des données lors d'un crash ou d'une perte de courant. Disons que votre système est entrain d'écrire un ficheir sur le disque et qu'il y a une coupure de courant. Sans un journal, votre ordinateur ne saura pas si le fichier a été écrit complètement sur le disque. Le fichier restera sur le disque, corrompu.

![](.gitbook/assets/createpartition-journaling.jpg)

Avec un journal, votre ordinateur notera qu'il était entrain d'écrire un fichier sur le disque dans le journal, écrira ce fichier et enlèvera cette tâche du journal. Si le courant est coupé pendant l'écriture d'un fichier, Linux vérifiera le journal du système de fichier lorsqu'il redémarre et reprend toutes les tâches partiellement complètées. Cela évite la perte de données et la corruption de fichier.

La journalisation ralentit un peu les performances d'écriture sur le disque, mais c'est un pis-aller sur un ordinateur de bureau.

{% hint style="success" %}
Quel est le système de fichier parfait pour vous?

 Généralement, un système de fichier avec journalisation est préféré au lieu d'un système sans journalisation lorsque vous avez le choix. Vous pouvez aussi condiférer lequel choisir en fonction du support de  _Security Enhanced Linux_ (ou SELinux) par le système de fichier. 
{% endhint %}

Voici un résumé rapide des types que vous devez connaitre pour l'examen LPI **:**

| Format                                    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ----------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <p><strong>ext2</strong></p><p>(1993)</p> | <p>Le système de fichier ext2 (aussi connu comme le <em>second système de fichier étendu second</em>) a été developpé pour traiter des problèmes sur le système de fichier Minix utilisé dans les versions précédente de Linux. Il a été beaucou utilisé sur Linux pendant de nombreuses années. Il n'y a pas de journalisation dans ext2, et il a été largement remplacé par ext3 et plus récémment par ext4.</p><p></p><ol><li>La taille maximum des fichiers est de <strong>16GB – 2TB</strong>.</li></ol><p>* Il était utilisé pour les médias de stockage Flash comme les <strong>clés USB</strong>, <strong>SD Card</strong> etc.</p>             |
| <p><strong>ext3</strong></p><p>(2001)</p> | <p>Le système de fichier ext3 ajoute la possibilité de journalisation au systèm de fichiers standard ext2 et est une évolution vers un système de fichier vraiment fiable. Il offre des performances raisonnable dans la plupart des conditions et est encore amélioré. Comme il a jouté la journalisation sur le système de fichier éprouvé ext2, il est possible de convertir un système de fichiers ext2 existant vers ext3 et même de revenir en arrière si nécessaire.</p><ol><li>Taille maximum des fichiers <strong>16GB – 2TB</strong>.</li><li>a été intégré au <strong>Noyau 2.4.15</strong> avec la fonctionnalité de journalisation</li></ol> |
| <p><strong>ext4</strong></p><p>(2008)</p> | <p>Le système de fichier ext4 a démarré comme une extension de ext3 pour gérer les demandes pour des ficheiers plus large en augmentant la limit de stockage et en améliorant les performances. Certains de ces changement depuis ext3 sont ::</p><ol><li>Taille maximum des fichiers <strong>16GB to 16TB</strong>.</li><li>a été inclu dans le <strong>noyau  2.6.28</strong>.</li><li>Le système de fichier Ext4 a des options pour désactiver la fonctionnalité de journalisation.</li><li>D'autres fonctionnaliés comme <strong>Fast FSCK</strong> etc.</li></ol>                                                                             |
| **XFS**                                   | XFS est un système de fichier avec journalisation. Il vient avec des fonctionnalités solides et est optimisé pour la scalabilité. XFS met en cache de manière aggressive les données en transit dans la RAM, ce qui est très bien si vous avez du courant sans interruption |
| **btrfs**                                 | btrfs (B-Tree file system) a été initialement développé par Oracle(GPL). C'est un nouveau système de fichier copy-on-write pour Linux qui a pour but d'ajouté des fonctionnalités avancés (snapshots, compression ...) an se concentrant sur la tolérance à la panne, la réparation et l'administration simplifié. Conçu pour gérer des fichiers vooumineux de manière efficace et gérer des systèmes de fichiers divisés sur plusieurs périphériques |
| **vfat**                                  | <p>(aussi connu comme <em>FAT32</em>) pas de journalisation, manque de nombreuses fonctionnalités requises pour un système de fichiers complet pour Linux. Utilise poru échanger des données entre les systèmes Windows et Linux . Ne <strong>pas</strong> utiliser ce système de fichier, sauf pour partager des données. </p><p>*Si vous extrayer les fichiers d'une archive zip ou tar sur un disque vfat, vous perdrez les permissions comme la permission d'exécuter, et vous perdrez tout lien symbolique qui ont été stockés dans l'archive.</p>                                                                                          |
| **swap**                                  | Espace Swap doit être formaté pour utiliser en tant qu'espace Swpa, mais il n'est généralement pas considéré comme système de fichier |

Nous devons créer un système de fichier avant d'utiliser tout périphérique de stockage connécté à un ordinateur Linux.

### Partitionnement


{% hint style="danger" %}
Avant de commencer à modifier les partitioins, il y a des points important à se rappeler. Vous risquer de perdre vos données existantes si vous ne suivez pas ce instructions : 

1. Sauvegarder ailleurs vos données importantes avant de commencer 
2. Ne modifier pas les partitions qui sont utilisées 
3. Connaissez vos outils
4. Arrêtez vous si vous faites une erreur
{% endhint %}

Linux utilise la commande `mkfs` pour créer des systèmes de fichier et la commande `mkswap` pour créer des espaces swap.


### mkfs

La commande mkfs (make filesystem) est utilisée pour créer un système de fichier.

La commande `mkfs` est actuelleemnt un frontal pour plusieurs commandes spécifiques au systèmes de fichier comme `mkfs.ext3` pour ext3, `mkfs.ext4` pour ext4 et `mkfs.btrfs` pour btrfs.

```
root@ubuntu16-1:~# mkfs
mkfifo        mkfs          mkfs.ext2     mkfs.ext4dev  mkfs.msdos
mkfontdir     mkfs.bfs      mkfs.ext3     mkfs.fat      mkfs.ntfs
mkfontscale   mkfs.cramfs   mkfs.ext4     mkfs.minix    mkfs.vfat

root@ubuntu16-1:~# ls /sbin/mk*
/sbin/mkfs         /sbin/mkfs.ext2  /sbin/mkfs.ext4dev  /sbin/mkfs.msdos
/sbin/mkfs.bfs     /sbin/mkfs.ext3  /sbin/mkfs.fat      /sbin/mkfs.ntfs
/sbin/mkfs.cramfs  /sbin/mkfs.ext4  /sbin/mkfs.minix    /sbin/mkfs.vfat

```

Différentes variantes de la commande : mke2fs, mkfs.ext2, et mkfs.ext3 ont toute leur propre fichier, alors que mkfs.msdos et mkfs.vfat sont généralement des liens symboliques vers  mkdosfs.

Afin de consstruire le système de fichier en utilisant la commande mkfs, les arguments requis sont filesystem-type et device-filename:

```
mkfs [options] [-t type fs-options] device [size]
```

Nous pouvons aussi utiliser la commande mkfs._fstype_  `mkfs.ext3 /dev/sdb1 ` ou nous pouvons utiliser mkfs via  l'option `-t ` pour spécifier le format `mkfs -t ext3 /dev/sdb1` et le deux auront le même résultat.

```
root@ubuntu16-1:~# mkfs -t ext3 /dev/sdb1
mke2fs 1.42.13 (17-May-2015)
Creating filesystem with 5242624 4k blocks and 1310720 inodes
Filesystem UUID: fb1e0a9a-04c3-4985-80e9-132f01731c9f
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
	4096000

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done  c

root@ubuntu16-1:~# 
```

Si nous voulons assigner une étiquette à la partition pendant le formatage, nous devons utiliser -L labelname avec :  `mkfs -t ext3 -L MyData /dev/sdb1`

{% hint style="info" %}
Pour monter des partitions formaté nous pouvons utiliser l'étiquette de la partition ou son **UUID**.  **UUID** _qui est un identifiant unique utilisé dans les paritions pour identifier les partitions de manière unique_. Pour obtenir l'UUID des partitions créé essayez : `blkid /dev/sdb1`
{% endhint %}

| Partition | Format Type | Sample Command                    | Notes                                                             |
| --------- | ----------- | --------------------------------- | ----------------------------------------------------------------- |
| /dev/sdb1 | ext4        | mkfs -t ext4 -L data /dev/sdb1    |  Assigns Label,same as mkfs.ext4                                  |
| /dev/sdb2 | xfs         | mkfs -t xfs -i size=512 /dev/sdb2 | telling it to have larger inodes (normal is 256) it helps Selinux |
| /dev/sdc1 | ReiserFS    | mkfs -t reiserfs /dev/sdc1        | Or you can use `mkreiserfs` command.                              |
| /dev/sdc2 | vFAT        | mkfs -t vfat /dev/sdc2            | Or you can use `mkfs.vfat` command                                |
| /dev/sdc3 | Btrfs       | mkfs -t btrfs /dev/sdc3           | Or you can use  `mkfs.btrfs` command                              |

### mkswap

 La commande `mkswap` créer un espace swap sur un périphérique ou dans un fichier. 

```
mkswap [options] device [size]
```

L'argument device est généralement une partition disque (something like /dev/sdb1) mais peut aussi être un fichier. 

Le noyau Linux ne chercher pas d'ID de partition, mais de nombreux scripts d'installation supposeront que les partition de type hex 82 (LINUX_SWAP) sont des partitions swap.

Donc pour créer un espace swap, créer d'abord une partition en utilisant fdisk avec une partition de type 82  puis utiliser mkswap:

```
root@ubuntu16-1:/# mkswap /dev/sdb1
mkswap: /dev/sdb1: warning: wiping old ext3 signature.
Setting up swapspace version 1, size = 20 GiB (21473783808 bytes)
no label, UUID=0dcd7e90-4b45-4d5f-808c-320f1e5ba8a3
```

Utiliser les partitions créées en tant qu'espace swap nécessite d'autres étapes que nous verrons plus tard.

C'est terminé!

.

.

.

[https://wiki.manjaro.org/index.php?title=Some_basics_of_MBR_v/s_GPT_and_BIOS_v/s_UEFI](https://wiki.manjaro.org/index.php?title=Some_basics_of_MBR_v/s_GPT_and_BIOS_v/s_UEFI)

[https://developer.ibm.com/tutorials/l-lpic1-104-1/](https://developer.ibm.com/tutorials/l-lpic1-104-1/)

[https://www.geeksforgeeks.org/file-systems-in-operating-system/](https://www.geeksforgeeks.org/file-systems-in-operating-system/)

[https://likegeeks.com/linux-file-system/](https://likegeeks.com/linux-file-system/)

[https://developer.ibm.com/tutorials/l-lpic1-102-1/](https://developer.ibm.com/tutorials/l-lpic1-102-1/)

[https://www.computerhope.com/jargon/p/partition.htm](https://www.computerhope.com/jargon/p/partition.htm)

[https://www.geeksforgeeks.org/fdisk-command-in-linux-with-examples/](https://www.geeksforgeeks.org/fdisk-command-in-linux-with-examples/)

[https://www.tecmint.com/what-is-ext2-ext3-ext4-and-how-to-create-and-convert-linux-file-systems/](https://www.tecmint.com/what-is-ext2-ext3-ext4-and-how-to-create-and-convert-linux-file-systems/)

[https://www.howtogeek.com/howto/33552/htg-explains-which-linux-file-system-should-you-choose/](https://www.howtogeek.com/howto/33552/htg-explains-which-linux-file-system-should-you-choose/)

[https://developer.ibm.com/tutorials/l-lpic1-104-1/](https://developer.ibm.com/tutorials/l-lpic1-104-1/)

[https://jadi.gitbooks.io/lpic1/content/1041\_create_partitions_and_filesystems.html](https://jadi.gitbooks.io/lpic1/content/1041\_create_partitions_and_filesystems.html)

.
