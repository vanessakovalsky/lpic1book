# 102.1. Modèle de conception d'un disque dur

## **102.1 Modèle de conception d'un disque dur**

**Poids:** 2

**Description:** Les candidats doivent être capable de concevoir le schema de partitionnement d'un disque pour un système Linux.

**Connaissances clés:**

* Allouer le système de fichier et l'espace swap pour séparer les partitions ou les disques
* Adapter la conception pour correspondre à l'usage du système
* S'assurer que la partition /boot est conforme aux pré-requis de l'architecture matériel pour le démarrage
* Connaissances des fonctionnalités basic de LVM

**Concepts et Utilitaires:**

* / (root) filesystem
* /var filesystem
* /home filesystem
* /boot filesystem
* swap space
* mount points
* partitions

Dans cette petite leçon nous ferons le tours de la structure d'un disque dur et comment les données sont stockées sur eux, puis nous irons voir le modèle de disque de linux.

### Pistes de siques, cylindre et secteurs

Un disque est divisé en pistes, cylindres et secteurs.

![](.gitbook/assets/disklayout-diskstructure.jpg)

Une **piste** est une portiion du disque qui passe sous une tête unique stationnaire pendant la rotation du disque. Un anneau est large de 1 bit.

Un **cylindre** est comprit dans l'ensemble des pistes décrit par toutes les têtes (sur des plateaux séparés) sur une position de recherche unique. Chaque cylindre est équidistant du centre du disque. Une piste est divisé en segments de secteurs, ce qui est l'unité basique de stockage.

Un **secteur**, est la plus petite unité physique de stockage sur un disque, il fait presque toujours 512 bytes car 512 st un multiple de 2 ( 2 puissance 9). Le nombre 2 est utilisé car il y a deux état dans le plus basique des langages de l'ordinateur : on et off.

### Partitions

Le partitionnement du disque ou division du disque est la création d'une ou plusieurs régions sur un disque dur ou un autre stockage secondaire,  **pour que le système d'exploitation puisse gérer les information dans chaque région séparément**. Ces régions sont appelées partitions.

Le disque stocke la localisation de l'** information à propos des partitions' et leur tailles** dans une zone appelé la **table de partition** que le système d'opération lead avant tout autre partie du disque. Chaque partition apparait alors dans le système d'exploitation comme un disque "logique" distinct qui utilise une partie du disque.

```
[root@centos7-1 ~]# lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   50G  0 disk 
├─sda1            8:1    0    1G  0 part /boot
└─sda2            8:2    0   49G  0 part 
  ├─centos-root 253:0    0 45.1G  0 lvm  /
  └─centos-swap 253:1    0  3.9G  0 lvm  [SWAP]
sr0              11:0    1 55.7M  0 rom  /run/media/payam/VMware Tools
```

Lorsque vous faite une installation il y a normalement au moins deux partitions qui ont besoin d'être créé :

* **/ **(root): dossier racine qui contient la distribution Linux.
* **Espace Swap**

### **Qu'est ce que l'espace swap?**

L'espace Swap dans Linux est utilisé lorsque la totalité de la mémoire physique disponible (RAM) est occupé. Si le système a besoin de plus de ressources mémoires et que la RAM est pleine, les pages inacties en mémoire sont déplacer dans l'espace swap. Alors que l'espace swap peut aider les machines avec de faibles capacité de RAM, il ne doit pas être considéré comme un remplacemnet pour plus de RAM. L'espace Swap est placé sur le disque dur, qui a un temps d'accès plus lent que la mémoire physique.

**L'espace Swap peut être** une **parition dédié au swap** (recommendé), un **fichier swap**, ou une **combinaison de paritions swap et de fichier swap**.

Swap doit être équivalent à 2X la RAM physique pour le RAM physique jusqu'à 2GB et environ 1.5X la RAM physique pour la RAM de plus de 2GB. Cepenant c'est une recette ancienne et cela dépend du type de système dont nous parlons.

### **Point de montage**

Toutes les **partitions sont attaché au système via un point de montage**. Le point de montage défini la place d'un ensemble particulier de donnée dans le système de fichier. Généralement, toutes les partitions sont connecté au travers de la partition racine. Sur cettte parition, qui est indiqué avec le slash (/), les dossiers sont créé. Ces dossiers vides sont les points de départ des partitions attachés à eux.

```
[root@centos7-1 ~]# df -h
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/centos-root   46G  4.4G   41G  10% /
devtmpfs                 1.9G     0  1.9G   0% /dev
tmpfs                    1.9G     0  1.9G   0% /dev/shm
tmpfs                    1.9G   26M  1.9G   2% /run
tmpfs                    1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/sda1               1014M  179M  836M  18% /boot
tmpfs                    378M   32K  378M   1% /run/user/1000
/dev/sr0                  56M   56M     0 100% /run/media/pabros/VMware Tools
tmpfs                    378M     0  378M   0% /run/user/0
```

Pendant le démarrage du système, toutes les partitions sont montés (et seront décirte dans le fichier /etc/fstab). Certaines partitions ne sont pas monté par défaut, par exemple si elles ne sont **pas connectés en permanence au système, comme les stockage usb**. S'il est bien configuré, le périphérique sera monté dès que le système sera informé qu'il est connecté, ou il peut être monté par l'utilisateur.

### Système de fichiers

**Un système de fichier est l'ensemble des méthodes et des structures de données qu'un système d'exploitation utilise pour garder une trace des fichiers sur un disque ou une partition**; c'est, d'une certaine manière, la manière dont les fichiers sont organisés sur le disque. Sans un système de fichier, les informations placés sur un media de stockage sera un ensemble important de données sans moyen de savoir où est quelle information, où elle s'arrête et où commence la prochaine.

Des systèmes de fichiers différents ont une structure différente pour déterminer où sont les données et indexer l'information qui est stockée :

* **FAT32**: FAT32 est un ancien système de fichier de Windows, mais il est encore utilisé sur les médias amovibles - seulement les plus petit. Les disque dur externes de plus de 1TB sont formatté avec NTFS
* **NTFS**: les versions modernes de Windows — depuis Windows XP — utilise le système de fichier NTFS pour leur système de partitions. Les disques externes peuvent être formatté soit en  FAT32 ou en  NTFS.
* **HFS**+: Macs utilise HFS+ pour ses partitions internes, et aime formater les disques externe également avec  HFS+. 
* **Ext2/Ext3/Ext4**: Vous verrez souvnet les systèmes de fichier Ext2, Ext3, et Ext4 sur Linux.
* **Btrfs**: Btrfs — “better file system” — est un nouveau système de fichier Linux qui est encore en développement. Ce n'est pas celui utilisé par défaut sur la plupart des distribution Linux, mais il remplacera probablement Ext4 un jour. L'objectif est de fournir des fonctionnalités supplémentaire pour permettre à Linux de se mettre à l'échelle sur des stockage plus important.
* **Swap**: Sur Linux, le système de fichier “swap” n'est pas vraiment un système de fichier. Une partition formatté comme “swap” peut seulement être utilisé comme espace swap par le système d'exploitation 

#### Linux Directory Structure

Linux uses a hierarchical file system structure, much like an upside-down tree, with root (/) at the base of the file system and all other directories spreading from there. The linux directory consists of many file systems that can be on many devices even on many servers.

```
[root@centos7-1 ~]# tree / -L 1
/
├── bin -> usr/bin
├── boot
├── dev
├── etc
├── home
├── lib -> usr/lib
├── lib64 -> usr/lib64
├── media
├── mnt
├── opt
├── proc
├── root
├── run
├── sbin -> usr/sbin
├── srv
├── sys
├── tmp
├── usr
└── var

19 directories, 0 files
```

Lets explain what they are for:

* **/** (root): the root filesystem, mounted before the kernel loads the first process. The bootloader tells the kernel what to use as the root filesystem (it's usually a disk partition but could be something over the network).
* **/bin** : All the executable binary programs (file) required during booting, repairing, files required to run into single-user-mode, and other important, basic commands.
* **/boot** : Holds important files during boot-up process, including Linux Kernel.
* **/dev** : an in-memory filesystem where device files are automatically created by udev based on available hardware.Contains device files for all the hardware devices on the machine e.g., cdrom, cpu, etc
* **/etc** : Contains Application’s configuration files, startup, shutdown, start, stop script for every individual program.
* **/home** : Home directory of the users. Every time a new user is created, a directory in the name of user is created within home directory which contains other directories like Desktop, Downloads, Documents, etc.
* **/lib** : The Lib directory contains kernel modules and shared library images required to boot the system and run commands in root file system.
* **/media **: Temporary mount directory is created for removable devices .
* **/mnt** : Temporary mount directory for mounting file system.
* **/opt** : Optional is abbreviated as opt. Contains third party application software.
* **/proc** : A virtual and pseudo file-system which contains information about running process with a particular Process-id aka pid.
* **/root **: This is the home directory of root user and should never be confused with ‘/‘
* **/run** : This directory is the only clean solution for early-runtime-dir problem.
* **/sbin** : Contains binary executable programs, required by System Administrator, for Maintenance.
* **/srv** : Service is abbreviated as ‘srv‘. This directory contains server specific and service related files.
* **/sys** : Modern Linux distributions include a /sys directory as a virtual filesystem, which stores and allows modification of the devices connected to the system.
* **/tmp **:System’s Temporary Directory, Accessible by users and root. Stores temporary files for user and system, till next boot.
* **/usr** : Contains executable binaries, documentation, source code, libraries for second level program.
* **/var** : Stands for variable. The contents of this file is expected to grow. This directory contains log, lock, spool, mail and temp files.

{% hint style="danger" %}
Almost all of linux directories can be a separated partition, except /etc directory because it contains scripts which are required during boot process so it can be mounted with / partition and accessible at the very beginning boot process.
{% endhint %}

### Designing Hard Disk Layout

Of course just because we can partition a disk doesn't mean we should! The more partitions we create, the more there is to manage. So we shouldn't make extra partitions unless we consider the extra work they create to be worth the additional protections they provide.

If the root partition runs out of space the system will crash. If some non-root partition runs out of space, the system will remain up and the System Administrator can login and fix things. Thus the directories such as /home, /tmp, and /var that users can easily fill with downloads, email, etc., are prime candidates for extra partitions. So are any other directories that might grow (directories for FTP uploads, database files, etc.).

If the partition containing the log files runs out of space, the system won't be able to write any log messages that could help an System Administrator to determine what went wrong. On the other hand some rapidly recurring error (or attack) can cause log files to grow very large very quickly, filling the partition containing them. So the directory containing log files, (usually /var/log), is a good choice for a separate partition.

All in all try to follow vendor-recommended standard disk layout if not, be smart and do partitioning based on the server type and behaviour of your application(s).

{% hint style="success" %}
#### Blocks vs. Sectors

**A sector is a physical spot on a formatted disk that holds information.** When a disk is formatted, tracks are defined (concentric rings from inside to the outside of the disk platter). Each track is divided into a slice, which is a sector. On hard drives and floppies, each sector can hold 512 bytes of data.

**A block, on the other hand, is a group of sectors that the operating system can address (point to). **A block might be one sector, or it might be several sectors (2,4,8, or even 16). The bigger the drive, the more sectors that a block will hold.

**So why are there blocks? Why doesn't the operating system just point straight to the sectors?** Because there are limits to the number of blocks, or drive addresses, that an operating system can address. By defining a block as several sectors, an OS can work with bigger hard drives without increasing the number of block addresses.
{% endhint %}

up to now we have talked about partitions, partitions are cool but partitions are fixed sized and that is not easy to resize in some cases. On a server we even need **more flexibility**. That is why **LVM** was invented.

### LVM

Logical volume management (LVM) is a **form of storage virtualization** that offers system administrators a more **flexible** approach to managing disk storage space than traditional partitioning.

There are 3 concepts that LVM manages:

* **Logical Volumes (LV)**: A Logical Volume is the conceptual equivalent of a disk partition in a non-LVM system. Logical volumes **are block devices which are created from the physical extents present in the same volume group**. File systems are built on top of logical volumes.
* **Volume Groups (VG)**: A Volume Group **gathers together a collection of Logical Volumes and Physical Volumes into one administrative unit**. Volume group is divided into fixed size physical extents.
* **Physical Volumes (PV)**: Each Physical Volume can be a disk partition or whole disk.

![](.gitbook/assets/disklayout-lvm.jpg)

Logical Volume Management (LVM) makes it easier to manage disk space. If a file system needs more space, it can be added to its logical volumes from the free spaces in its volume group and the file system can be re-sized as we wish. If a disk **starts to fail**, replacement disk can be registered as a physical volume with the volume group and the logical volumes extents can be migrated to the new disk without data loss.

**How LVM works?**

It works by chunking the physical volumes (PVs) into physical extents (PEs). The PEs are mapped onto logical extents (LEs) which are then pooled into volume groups (VGs). These groups are linked together into logical volumes (LVs) that act as virtual disk partitions and that can be managed as such by using LVM.

![](.gitbook/assets/disklayout-lvmdetails.jpg)

{% hint style="danger" %}
There is one thing that we should know about LVM, we can not put intire server in LVM! Because there is a /boot directory and /boot directory must be available at the moment of booting, so it must be seen from master boot record (or GUID partition).

And in order to see /boot directory at the moment of booting it should always be on a traditional partition.
{% endhint %}

![](.gitbook/assets/disklayout-lvmboot.jpg)

That is all.

.

.

.

.

Sources:

[https://www.studytonight.com/operating-system/secondary-storage](https://www.studytonight.com/operating-system/secondary-storage)

[https://www2.cs.duke.edu/csl/docs/sysadmin_course/sysadm-30.html](https://www2.cs.duke.edu/csl/docs/sysadmin_course/sysadm-30.html)

[http://www.ntfs.com/hard-disk-basics.htm](http://www.ntfs.com/hard-disk-basics.htm)

[https://en.wikipedia.org/wiki/Disk_partitioning](https://legacy.gitbook.com/book/borosan/lpic1-exam-guide/edit#)

[https://www.tldp.org/LDP/sag/html/filesystems.html](https://www.tldp.org/LDP/sag/html/filesystems.html)

[https://en.wikipedia.org/wiki/File_system](https://legacy.gitbook.com/book/borosan/lpic1-exam-guide/edit#)

[https://searchstorage.techtarget.com/definition/file-system](https://searchstorage.techtarget.com/definition/file-system)

[https://www.howtogeek.com/196051/htg-explains-what-is-a-file-system-and-why-are-there-so-many-of-them/](https://www.howtogeek.com/196051/htg-explains-what-is-a-file-system-and-why-are-there-so-many-of-them/)

[https://www.centos.org/docs/5/html/5.2/Deployment_Guide/s1-swap-what-is.html](https://www.centos.org/docs/5/html/5.2/Deployment_Guide/s1-swap-what-is.html)

[https://www.linuxtopia.org/online_books/introduction_to_linux/linux_Mount_points.html](https://www.linuxtopia.org/online_books/introduction_to_linux/linux_Mount_points.html)

[https://unix.stackexchange.com/questions/12040/what-mount-points-exist-on-a-typical-linux-system](https://unix.stackexchange.com/questions/12040/what-mount-points-exist-on-a-typical-linux-system)

[https://www.tutorialspoint.com/unix/unix-file-system.htm](https://www.tutorialspoint.com/unix/unix-file-system.htm)

[https://www.howtogeek.com/117435/htg-explains-the-linux-directory-structure-explained/](https://www.howtogeek.com/117435/htg-explains-the-linux-directory-structure-explained/)

[https://wpollock.com/AUnix1/Partitioning.htm](https://wpollock.com/AUnix1/Partitioning.htm)

[http://www.alphaurax-computer.com/computer-tips/hard-drive-knowledge-blocks-vs-sectors](https://legacy.gitbook.com/book/borosan/lpic1-exam-guide/edit#)

[https://searchdatacenter.techtarget.com/definition/logical-volume-management-LVM](https://searchdatacenter.techtarget.com/definition/logical-volume-management-LVM)

[https://wiki.ubuntu.com/Lvm](https://wiki.ubuntu.com/Lvm)

[https://www.thegeekdiary.com/redhat-centos-a-beginners-guide-to-lvm-logical-volume-manager/](https://www.thegeekdiary.com/redhat-centos-a-beginners-guide-to-lvm-logical-volume-manager/)

[https://www.tecmint.com/create-lvm-storage-in-linux/](https://www.tecmint.com/create-lvm-storage-in-linux/)
